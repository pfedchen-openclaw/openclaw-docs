# PHASE-0-LESSONS.md

> **Status: FINALISED / LOCKED (Session 6a.35, 2026-06-02).** Curated from the 6a.32 first draft, reconciled against MASTER-ARCHITECTURE-v6. **Required reading at every 6b+ session opener.**
>
> Source of truth for the detail behind each item is DEVIATIONS-LOG.md (cited inline by code). This file is the distilled, navigable layer over it; live ground truth is PROJECT-STATE-OF-NATION.md + PROJECT-RUNBOOK.md.

---

## PART A — Carry-forward operational rules (normative)

Each rule: **name** — the rule — *(source)* — worked example.

1. **Verify what a scheduled job DOES, not just that it loads.** A `launchctl print` of loaded state is necessary but not sufficient; check the job's log-tail + output-existence for evidence of a clean fire. *(F63 6a.30 CORRECTION; F68; F70)* — security-audit was "loaded" for weeks while silently failing `env: node: No such file or directory` every fire; only the log-tail revealed it (F68). At 6a.35 the live vault-backup probe confirmed the fire via `last exit code = 0` + log-tail, not loaded-state alone.

2. **launchctl error parsing: `head -3` minimum, NEVER `head -1`.** Tahoe `launchctl` emits `Bad request.` as a cryptic line-1; the real diagnostic is line-2. *(F69)* — confirmed again at 6a.35: the two descope probes each returned line-1 `Bad request.`, line-2 `Could not find service …` — `head -1` would have hidden the confirmation that D24 held.

3. **Verify the launchd-spawn environment BEFORE wiring automation.** A launchd job does not inherit your interactive shell's PATH/env/creds; re-verify PATH, env vars, and credential availability in the actual spawn context. *(F63 → F68 → F70 lineage)* — F68 PATH gap (`/opt/homebrew/bin` absent from launchd's minimal PATH) is now applied prophylactically to every launchd job touching Homebrew binaries; F70 caught the op-CLI gap *before* silent failure.

4. **Headless-Mini mutation discipline + durable launchd removal.** User-session-state mutations on the headless Mini must be SSH-completable or sequenced to avoid GUI-only recovery; prefer the SSH-completable path. Durable launchd-job removal requires a **non-`*.plist` suffix** — `bootout` alone is undone on next GUI login because login re-loads every `*.plist`. *(F67 + 6a.31 sub-lesson)* — descoped jobs renamed `<label>.plist.disabled-D24`; proven durable through a forced power-cycle reboot (D24).

5. **A credential present on one plumbing path is NOT automatically on the consumer's path.** Verify the specific resolution path the consuming subsystem uses; provision per-subsystem. *(F61; v6 §3.1/§4.1)* — the OpenAI key in `secrets.json` did NOT satisfy the embedding subsystem, which reads per-agent `auth-profiles.json`; `openai:default` had to be provisioned into all 10 agent dirs. **Any new agent created via `openclaw agents add` (6b/6c) inherits only `anthropic:default` and needs `openai:default` provisioned before its first embedding call.**

6. **Auth-profiles inheritance is materialise-once, not live.** Any credential rotation must explicitly update each per-agent auth-profile copy, not just the root. *(F25)* — applies in every future session touching `agents/*/agent/`.

7. **`.last-good` is daemon-promoted and byte-identical-to-live; characterise divergence, do not assume benign.** *(F60/F62)* — every pre-flight re-verifies `.last-good` cleanliness; `7401`/`mem=1` would mean poisoning recurred → STOP.

8. **F59 credential hygiene.** Never echo a secret value; emit length + sha12 only. `op read --no-newline | sha256sum` is the canonical verify (plain `op read` appends a trailing newline). op verbs that touch credential material (`item create`/`edit`) print field contents to stdout by default — redirect to `/dev/null` and verify via round-trip. *(F38, F51, F59)* — F59 itself is the cautionary tale: an `env | grep GOG_KEYRING_` leaked the keyring password into a transcript.

9. **Chat-layer TLD linkification defence.** The chat interface rewrites dotted-TLD tokens (`.md`, `.json`, email-shaped strings) and URLs into auto-links, which corrupt copy-paste. Use shell-variable indirection in emitted commands (build dotted filenames / IPs / service labels from variables); use display-immune SHA for verification. *(F14)*

10. **zsh does not treat `#` as a comment.** With `interactive_comments` off (the default on the Mini's zsh), a pasted line beginning with `#` is executed as a command and errors. **Never put an inline `#` comment in any Mini command block** — put explanation in prose or use `echo` separators. *(working protocol; sibling of F14)* — surfaced 6a.33, recurred 6a.34 (cost two errors in one block); the rule is doubly earned.

11. **F17 staging discipline.** Stage config edits at `~/.openclaw/openclaw.json.new` (NEVER `/tmp` — `mv` from `/tmp` regresses mode + group); apply via `mv + chmod 600 + chgrp staff` in one block. *(F17)*

12. **Per-version empiricism; schema-valid ≠ runtime-implemented.** Run `--help` before extrapolating CLI syntax; exercise any schema-sourced capability at runtime before trusting it. *(F20-safe; S6)* — `memorySearch.provider:"local"` is in the 2026.4.22 schema but has no runtime adapter (hard-throws) — corrected in v6 PART 5; `op whoami --raw` flag is absent in this build; four v5-template paths were schema-obsolete (S6, folded into v6).

13. **Display-immune verification.** Sensitive or chat-fragile data verified via sha-12 + byte length, never raw echo. *(F14, F59)*

14. **SHA-verified delivery + selftest-first for state-changing scripts.** Deliver scripts as files (not heredoc paste), state the canonical sha256, verify on-Mini before running, run `--selftest` before the live run. *(F60 hygiene)* — `sanity_6a32.py` followed this end-to-end (sha `fde00538…`, reused unchanged at 6a.35).

15. **launchctl domain semantics.** `bootstrap`/`bootout` are GUI-Terminal-only (SSH → exit 125, D20); `launchctl print` is SSH-safe. *(D20)*

16. **ClawHub trust gate.** Every non-bundled skill gets a source review before install (D2); very-new GitHub accounts auto-rejected regardless of skill quality. Bundled gating is `skills.allowBundled`; skills install **per-workspace**, so install + `skills-manifest.md` logging is inherently an agent-build operation. *(D2, F1, F8, F57)*

17. **Honest failure characterization.** Name failures as failures, not as principled tradeoffs. *(working protocol)* — surfacing problems plainly (the F70 cost correction, the F72 backup gap) is the floor, not the ceiling.

18. **Session close discipline.** Three canonical deliverables per session (DEVIATIONS-LOG update, closure doc, next opener); DEVIATIONS-LOG is **append-only / additive-only** with programmatic proof; append status segments to existing rows, never rewrite. **In-row appends grow BYTES, not LINES** — state the invariant as rows/lines unchanged, bytes up (e.g. 6a.35 Stage 0: F-rows 69 / D-rows 25 and 463 lines unchanged, +1,630 bytes). **Loss-audit consolidation edits via reverse-reconstruction-to-original** — reversing every edit must reproduce the source byte-identically (RUNBOOK refresh 6a.34; MASTER-ARCHITECTURE-v6 amend 6a.35, 27 reversible edits). Each session attaches only the immediately-preceding closure + DEVIATIONS-LOG + MASTER-ARCHITECTURE (now v6) + current opener (+ USER files when relevant).

---

## PART B — Surprise inventory (highest-leverage subset)

What Phase 0 taught us that we didn't expect. Each cross-refs its source row.

- **`.last-good` poisoning + daemon auto-promotion.** The gateway auto-promotes `openclaw.json.last-good` and can poison it; a rollback incident taught the byte-identical-to-live invariant (rule A-7). *(F60, F62)*
- **The embedding credential lives on a different path than the inference credential.** The single highest-value gotcha for 6b/6c: a key on the inference path does not reach the embedding subsystem. Generalised to rule A-5. *(F61)*
- **The F63 misdiagnosis lineage.** A launchd env-gap was first *misdiagnosed* (F63, corrected 6a.30), then manifested as the first *true* instance (F68, security-audit `node` PATH gap), then was *proactively prevented* before silent failure (F70, op-CLI). The system caught its own miss — that lineage is the origin of rules A-1 and A-3.
- **Tahoe `launchctl` "Bad request." leading line.** Cost real diagnosis time; now rule A-2; recurred (benignly, as expected) at 6a.35. *(F69)*
- **Headless login re-loads every `*.plist`.** A `bootout` is undone on next GUI login; durable removal needs a non-`*.plist` suffix (rule A-4). *(F67)*
- **Schema-valid ≠ runtime-implemented.** Four v5-template paths were obsolete vs the live 2026.4.22 schema (`tools.web.search.apiKey`, `${VAR}` interpolation, `tools.browser.maxConcurrent` retired, `memorySearch.provider:"local"` unimplemented). All corrected in MASTER-ARCHITECTURE-v6 PART 5. *(S6, F54)*
- **The headless-Mini friction cluster.** FileVault-on / no-auto-login (D11) is a deliberate, sound security posture, but it is the single biggest source of recurring GUI-only-recovery friction in Phase 0 (F67) and the GUI-only-bootstrap constraint (D20): nothing comes up pre-GUI-login after a reboot, though the gateway auto-recovers once unlocked (F45). Named as a standing operational cost, not a defect — now documented in v6 §4.12. *(D11, F45, F67, D20)*
- **op-CLI launchd auth is a paid-tier decision, not a $5 add-on.** 1Password Service Accounts require a Teams/Business plan, not the ~$5/user/mo the design assumed; on a personal plan they don't exist. Deferred — interactive backup is the Phase-0 answer (F70). Reopen when unattended backup becomes a real need (post-6b).

**Resolved during Phase 0 (kept for the record):** the config had no offsite backup → **fixed**, `~/.openclaw` is now a git repo pushed to private `openclaw-config` with a sanitised-snapshot model (F72, v6 §4.9/§4.11). STATE-OF-NATION had gone stale → **fixed**, rebuilt from ground truth at 6a.33. No-Apple-ID was found to beat a dedicated Apple ID for isolation → **adopted** (D25, v6 §4.1).

---

## PART C — Navigation

- **Design baseline:** MASTER-ARCHITECTURE-v6.md (supersedes v5 tombstone).
- **Live ground truth:** PROJECT-STATE-OF-NATION.md (conceptual) + PROJECT-RUNBOOK.md (operational).
- **Locked decisions:** DEVIATIONS-LOG D1–D25 (D-rows). **Findings/flags:** F1–F72 (F-rows).
- **Strategic risks:** S1–S11. **Maintenance cadence:** M1–M7. **Standing reminders:** SR-1 (operator-docs GitHub target + CoS sync, 6c), SR-2 (ICS export before any agent gains calendar write, ~6b PA build).

*(End — locked 6a.35. Re-open only to add a newly-earned carry-forward rule, with its source F/D code.)*
