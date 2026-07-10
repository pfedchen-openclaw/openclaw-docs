# SESSION 6c.32 — CLOSURE

_Lead: **the 6d ANDREAS build — reversible foundation**. Peter scoped it at open (AskUserQuestion): **"full reversible foundation"** — derive the live `workspace-cos/` briefs + build the S11 enforcement layer + the sessions_spawn per-window budget + the SR-1 audit script, stop **before** the irreversible/billed cluster (openclaw.json `cos` block, BotFather pairing, F26), and hand a ready-to-run package. F26 cost-OK when reached = **Sonnet smoke-test only** (never Opus — [[no-unauthorized-test-spend]]). All delivered: Andreas's brief-set is authored, the S11 host-mailbox/watcher is built and **14/14 dryrun-tested**, the spawn budget is enforced in the same watcher, and the SR-1 `carry-chain-audit.py` is built, self-tested, **dogfooded at this very close — where it surfaced (and I fixed) a false-negative bug in its own append-only check**. Findings **[P6-140]** (the build) + **[P6-141]** (a doc-currency drift). **£0 billed** (briefs authored directly; COS-PLAYBOOK + the audit script ran on Claude-Code subagents; S11 exercised purely in dryrun — no dispatch, no spend). **openclaw.json UNCHANGED all session** (`3267edd8b6aa`/10489B, live==last-good, A-7 clean — no `cos` block yet). GUI/Aqua context (`SSH_CONNECTION` empty, `gui/502` reachable). Nothing is live: the whole foundation is inert scaffolding until the Peter-gated 6d pairing._

## What this session did
1. **Stage 0 GREEN.** A-7 `3267edd8b6aa`/10489B 600/staff; pin 2026.4.22; levers `contextTokens=120000` + `browser.snapshotDefaults.mode=efficient`; deviations **P6-1…139 gap-free** (`re.findall` audit clean, only the known 21/22 dups); live Marie briefs **sha-identical to the 6c.31 opener** (AGENTS `2445fd82d3a1`, TOOLS `f4167b1c15c7`, HEARTBEAT `f580d9152b1e` — no drift, so `BRIEF-DERIVATION-ANDREAS.md` is a faithful base, P6-26 holds); `agents/cos/auth-profiles.json` pre-exists (6a scaffold), `workspace-cos/` was empty.
2. **★ Scope call (Peter, AskUserQuestion).** "Full reversible foundation" + "Sonnet smoke-test only" for F26. So: zero billed turns, zero openclaw.json edits, zero pairing this session — all reversible host-side authoring + scripting.
3. **★★ Live `workspace-cos/` briefs derived ([P6-140], P6-26 discharged).** AGENTS/TOOLS/HEARTBEAT from the resolved draft; SOUL derived from Marie's (inverted to meta / altitude-firewall / propose-not-execute + the German-Swiss CoS persona, security spine intact); COS-PLAYBOOK the net-new heavy playbook (7 sections); shared canon copied verbatim (the non-restricted USER-CONTEXT + the house voice guide — **not** the restricted personal file, Andreas is digest-only/meta); MEMORY/TASKS/DECISIONS + heartbeat-state seeds. All 0600.
4. **★★ S11 host-mailbox + watcher built ([P6-140]) — the forbidden-ordering requirement (S11 before A2A).** `scripts/a2a-watch.py` — per-agent outbox (P6-114) + host `shared/a2a/` mailbox (HALT + ledger.jsonl + per-day counters), all 5 layers (L1 envelope/cycle/TTL/anti-spoof, L2 budgets, L3 cost-breaker, L4 kill-switch, L5 ledger). **14/14 dryrun tests pass** — no dispatch, no spend. **INERT quadruple-guarded:** staged-not-bootstrapped plist + `A2A_MODE=dryrun` + HALT present (fail-closed) + A2A tools OFF in config. Schema doc `shared/a2a/README.md`.
5. **★ sessions_spawn per-window budget ([P6-140]).** Built into the same watcher (`type:"spawn"`, ≤3/3600s/agent); tested. The gate that must exist before spawn goes live is now enforced in code.
6. **★ SR-1 `carry-chain-audit.py` built + DOGFOODED ([P6-140]).** The hard-scripted mechanical close checks (gap/dangling, append-only, sha-completeness, carry-diff). Self-tested + negative-tested (it BITES: synthetic gap/dangling → FAIL; deleted lines → FAIL). **Run at this close it caught a false-negative in its own append-only check** (a cwd-vs-repo path-doubling → "no changes" when the log had 14 additions); **fixed** (absolute pathspec) and re-verified (+14/−0, append-only OK). This is exactly the drift SR-1 exists to prevent — caught on its first real use.
7. **★ Doc-currency finding [P6-141].** The docs repo is `openclaw-docs` (empirical: `_session-docs/` remote); v7 PART 10 §SR-1 + the Andreas draft said "config repo"/`openclaw-config`. Live Andreas briefs **corrected**; v7 flagged for Andreas's first doc-currency reconciliation (append-only snapshot — not rewritten here).
8. **★★ Adversarial pre-push self-audit + remediation [P6-142]** (Peter-requested, before the push). Two Claude-Code reviewers (code + v7 §S11/§SR-1 design) + my direct checks. **Robustness bugs FIXED & re-tested (a2a-watch 20/20):** a malformed request could **wedge the whole mailbox** (#1, per-request quarantine + type-guards), dryrun wrote a real HALT (#3, live-only + per-mode counters), `hop:true`/`0` slipped through (#5), counters weren't crash-durable + a live double-bill (#2, save-per-request + claim-before-dispatch), fleet ceiling == hub budget (design #5, 30→60), plus SR-1's append-only check **fail-open on a bad ref + vacuous-HEAD** (#11, now fail-closed) and carry-diff missing numbered lists (#10). **Design gaps FLAGGED for 6d (not fixed):** the L1 cycle-check vs per-pair-budget can't both be live for a direct reply — the agent-side envelope constructor + topology are unbuilt (#4, the #1 before-live gate); provenance is agent-asserted (design #2). **v7 self-audit answered (Peter's Q):** record additively, do NOT patch the v7 snapshot mid-build — the reconcile is Andreas's milestone job. No build/doc discrepancy created.

## Cost
**£0 billed.** No `openclaw agent` turns. Briefs authored directly; two Claude-Code subagents (COS-PLAYBOOK, the audit script) = my own operation, not billed. S11 tested in dryrun only ([P6-51] respected by construction). openclaw.json never touched.

## SHA anchors
**Build artefacts → `openclaw-config` (`~/.openclaw`):**
| file | before | after |
|---|---|---|
| `workspace-cos/AGENTS.md` (NEW) | — | **`bbff1a0184f4`/16729B** |
| `workspace-cos/TOOLS.md` (NEW) | — | **`13ef069624d0`/8434B** |
| `workspace-cos/HEARTBEAT.md` (NEW) | — | **`7a8a09d6872c`/3873B** |
| `workspace-cos/SOUL.md` (NEW) | — | **`7669d69566e1`/8560B** |
| `workspace-cos/COS-PLAYBOOK.md` (NEW; +[P6-142] flags) | — | **`670b101e00c8`/20474B** |
| `workspace-cos/MEMORY.md` (NEW) | — | **`d9ec7b085323`/2545B** |
| `workspace-cos/TASKS.md` (NEW) | — | **`6ccd7519ffa7`/685B** |
| `workspace-cos/DECISIONS.md` (NEW) | — | **`26423ab8d73f`/1206B** |
| `workspace-cos/USER-CONTEXT.md` (copy, non-restricted) | — | **`e9be26588752`/8403B** |
| `workspace-cos/reference/USER-VOICE-GUIDE.md` (copy) | — | **`bccc323fcd73`/14157B** |
| `workspace-cos/memory/heartbeat-state.json` (NEW seed) | — | **`c540b7728480`/395B** |
| `scripts/a2a-watch.py` (NEW; +[P6-142] hardening) | — | **`f3460ae17105`/19660B** |
| `scripts/ai.openclaw.a2a-watch.plist.staged` (NEW, staged) | — | **`666e5e868e6f`/2853B** |
| `scripts/carry-chain-audit.py` (NEW; +path fix +[P6-142] fail-closed/ordered-list) | — | **`375f8e9dc1e9`/15102B** |
| `shared/a2a/README.md` (NEW; +[P6-142] §Open) | — | **`ee882782c770`/5191B** |
| `shared/a2a/HALT` (sentinel, present=fail-closed) | — | present |
| `shared/a2a/ledger.jsonl` (empty) | — | 0B |

**Close deliverables → `openclaw-docs` (`_session-docs/`):**
| file | before | after |
|---|---|---|
| `PHASE-6-DEVIATIONS-LOG.md` | P6-1…139 | **+ P6-140, P6-141, P6-142 (append-only, 0 deletions)** |
| `PRE-COS-BACKLOG.md` | `e6303b6e9558`/29858B | **`499b9e39f18f`/31227B (+[P6-142] before-live gates)** |
| `SESSION-6c.32-CLOSURE.md` (this) | — | NEW |
| `SESSION-6c.33-OPENER.md` | — | NEW |
| `openclaw.json` | `3267edd8b6aa`/10489B | **UNCHANGED** `3267edd8b6aa`/10489B (A-7 clean) |
- **Live state:** gateway untouched; Marie/Charlotte briefs untouched; pa model `opus-4-7` untouched; no LaunchAgent bootstrapped (the a2a-watch plist is STAGED only).

## Carry-chain check ([P6-101]) — dogfooded via `carry-chain-audit.py`
- **CHECK 1 (gap/dangling):** PASS — 141 unique P6 codes, contiguous P6-1…P6-141, every reference resolves; 21/22 dups tolerated.
- **CHECK 2 (append-only):** PASS — vs the true 6c.31 baseline (`d48efae`) the log is **pure-additive (+15/−0)**, 0 lines removed/modified. _(Two self-bugs in this check surfaced + handled at this close — see [P6-140]: the path-doubling false-negative was fixed; and `--since` must be the prior-close ref, not `HEAD`, else a mid-close re-commit false-trips it. This close was squashed to ONE additive commit vs 6c.31.)_
- **CHECK 4 (carry-diff 6c.31→6c.33):** run at close (below) — every 6c.31 "Carried →" thread picked-up or consciously dispositioned.
- **Carry diff vs 6c.31 "Carried → 6c.32":** (a) **★★ 6d = BUILD ANDREAS** → **advanced** — the reversible spine (briefs + S11/spawn caps + SR-1) is built; the irreversible remainder (pairing, F26, A2A enable) is packaged → 6c.33; (b) **P6-134 organic multi-cc verify** → **carried** (still free-inspection; no Marie sends this session); (c) **⛔ ANDREAS remit (6d build-content)** → **carried** in `PRE-COS-BACKLOG` (unchanged; the transactional last-mile / browser gap / hard code-gates / bounce-sweep / bloat-cure remain 6d object-level work); (d) **optional reversible follow-ups** → carried (non-blocking); (e) **Sophie build open choices** (id `fam`/`sophie`, the Robyn/Victoria nanny reconciliation, family-scoped facts subset, PA-PLAYBOOK §Family shedding) → **consciously parked for Sophie's ~6e build** — not a 6d/Andreas concern; remains tracked in `BRIEF-DERIVATION-SOPHIE.md` + `PRE-COS-BACKLOG.md`. **Nothing silently dropped.**

## Carried → 6c.33 (the irreversible/billed cluster — Peter-gated, GUI/D20)
- **★★ PAIR ANDREAS (F2/F26).** Stage the openclaw.json `cos` agent block (model `opus-4-7`, the resolved tool grants with A2A **OFF**, sandbox `mode:all`/`scope:agent`, conservative cadence) as `openclaw.json.new` — **Peter applies via `!`** + gateway restart (deny-listed; GUI/D20); new BotFather bot + the 4-scope profile + `agents set-identity`; avatar (first male portrait, per the AVATAR stub, against the locked Charlotte exemplar). **F26 = one Sonnet smoke-test** (model override) through the real Telegram→gateway→agent path. Then bootstrap the **staged** a2a-watch plist in **dryrun** first; only after verifying the ledger over real traffic, flip `A2A_MODE=live` **and** remove `shared/a2a/HALT` (enable A2A incrementally, mirroring reply-watcher).
- **★ Wire the object-level remit incrementally** (his facet): the 08:00 cross-agent digest cron; the per-task cost tracker [P6-85]; SR-1 doc-currency ownership (incl. reconciling the v7 PART 10 `openclaw-config`→`openclaw-docs` drift, [P6-141]); the infra-currency steward loop (propose-only; `browser-dep-repair.sh` after any update). Each F26-verified before the next.
- **★ P6-134 organic verify** (free-inspection) — a real multi-cc send landing BOTH recipients on Marie's next such send.
- **⛔ ANDREAS 6d build-content** (`PRE-COS-BACKLOG.md`): transactional last-mile (CAPTCHA [P6-39] + hosted-field card-fill [P6-135] + the `pay-fill.sh` PATH bug); browser backend gap; hard code-level gates (pulse send-gate [P6-128] + marie@ approval-token); outbound-bounce/DSN sweep [P6-138]; the bloat/stick CURE; S9 suite.
- **Optional reversible (Peter-run, non-blocking):** `CHAT_RESET_TOKENS`→~70000; periodic reindex; orphan 1Password item `efrhqyifslubmapond7wb6mmee` cleanup; MEMORY.md RATIONALE-rotation; Charlotte/Marie avatars.
