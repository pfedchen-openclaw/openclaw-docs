# PRE-COS-BACKLOG — must-cover-before-Andreas
_Generated 6c.9 (2026-06-15). The single prioritised backlog of everything still-open or standing across **both** deviation logs — `DEVIATIONS-LOG.md` (frozen Phase-0→6a/6b, D/F/S/M/SR) and `PHASE-6-DEVIATIONS-LOG.md` (P6, sha `0127c93e9ba1`/139569B at this session's open) — plus the 6c.8→6c.9 empirical-week findings. Purpose: nothing load-bearing falls through the cracks when we author ARCHITECTURE v7 (6c.10) and build **Andreas** the Chief-of-Staff (6d)._

**How to read this.** Three lenses, kept separate on purpose:
- **§A Gating items** — must be DONE or DECIDED before the named milestone (6c.10 spec-authoring or 6d build-start). These are the real blockers.
- **§B Standing constraints** — invariants Andreas must *inherit and respect* (encode in his AGENTS.md/TOOLS.md/guardrails). Not "work", but a wrong call here is costly.
- **§C 6d build-content** — items that *are* the CoS's job (he does them once he exists); design in v7, build in 6d. Listed so we don't mistake them for blockers.
- **§D Maintenance cadences** (M-codes) — Andreas tracks, Peter executes.
- **§E Defer-past-6d** — explicitly out of scope until a real consumer exists.

Tag legend: **[MUST]** before 6d · **[NICE]** opportunistic/cheap · **[DEFER]** past 6d.

---

## §A — Gating items (real blockers; do/decide before the named gate)

### A.1 — Clearing in THIS session (6c.9)
- **P6-50 / P6-27 / P6-30 — comms & chat-hygiene** **[MUST]** — no-substance-no-message, no-signature-in-owner-DMs, show-working-without-technical-detail (+ suppress success tool-trace, P6-27 — config key still A-12-unverified). _Stage 2._
- **P6-57 — reliability carries** **[MUST]** — Colima socket-health-check, Marie 92 MB session compaction, dev/test key wiring, spend-visibility decision. _Stage 3._
- **P6-60 — wire the 4 key zones** **[MUST]** — `pa`→zone1 (no-op), `pro`→zone2, route Claude-Code/test turns to zone4; zone3 stays dormant→Andreas at 6d. _Stage 3 / build step 1._
- **P6-59 — watchdog sawtooth fix** **[MUST→commit]** — diagnosed+fixed+live (16h stable) but the script is **uncommitted**; commit + record. _Stage 3._

### A.2 — Gate is 6c.10 (must be settled to author the v7 Andreas spec)
- **P6-49 — Andreas CTO / engineering-steward remit** **[MUST]** — formalise in v7: PROPOSE-not-EXECUTE separation, respects the version pin, owns the SR-1 doc autopilot, must know the P6-40/P6-42 update-wipe. The spine of the v7 spec.
- **P6-48 — author ARCHITECTURE v7** **[MUST]** — the Andreas-ready CoS spec; this whole backlog is its input.
- **S6 — reconcile MASTER-ARCHITECTURE Part 5 vs live 2026.4.22 schema** **[MUST]** — ≥4 known obsolescence instances; do *before* authoring Andreas's config off the (partly stale) template, else we copy drift.
- **S11 — inter-agent rate-limiting / loop-protection design** **[MUST]** — **highest-risk gap.** Undesigned. A CoS that fans out to other agents is precisely the trigger case (counter location, ping-pong/fanout/burst detection, circuit-breaker, recovery). Must at least be *designed* in v7 before a fan-out-capable agent goes live. _The 6c.9 sawtooth is a live cautionary tale: a supervisor that can act on the system it watches must be unable to harm it (it starved pro's 4h beat for a day)._
- **SR-1 / D23 — doc-currency autopilot + GitHub-publish decision** **[MUST]** — STATE-OF-NATION + RUNBOOK ownership transfers to the CoS; the sync mechanism (push-on-change vs scheduled) and publish target are undecided. Resolve before the doc-ownership handoff.
- **P6-9 / P6-11 / P6-12 / P6-14 — lock voice/signature/wording conventions** **[MUST]** — the *conventions* (em-dash restraint, signature final-form, wording) must be locked so Andreas's authored prose inherits them. (The batch-*apply* to existing text is CoS-owned post-roster, P6-10 — that part is not a blocker.)
- **P6-26 — derive Andreas's AGENTS.md/TOOLS.md from CURRENT Marie/Charlotte** **[MUST]** — not a stale template; pull live helper-blocks, P6-22 loud-failure rule, capable image, F26 def-of-done, the locked voice conventions. Discharge at build, but the rule is set now.

### A.3 — Gate is 6d build-start (must be true before unattended Andreas runs)
- **F2 / F26 — Andreas device-pairing + end-to-end routing** **[MUST]** — pair his Telegram bot with the 4-scope profile (`operator.read/write/approvals/talk.secrets`); not live until exercised through a real turn (F26).
- **P6-45 — payment-layer guardrail** **[MUST as design constraint]** — gate value at the card issuer, not in the agent; bake into the autonomy-ladder design when L1 relaxes for Andreas.
- **P6-40 / P6-42 — encode `browser-dep-repair.sh` after every `openclaw update`** **[MUST]** — update wipes the playwright-core symlink + the empty-allowlist guard patch; Andreas owns the pin, so this must be in his guardrails.
- **F64 + F68 + F70 + D21/D24 — re-enable the descoped launchd jobs** **[MUST]** — calendar-backup + security-audit are `.disabled-D24`, blocked on: F64 fail-fast on token-refresh hang, F68 launchd-PATH env gap (`node`/abs paths), F70 `op` unauthenticated in launchd (no `OP_SESSION`). Unattended automation Andreas would own is blocked until these clear.
- **F44 / Phase G OAuth posture** **[MUST]** — 7-day refresh-token expiry under External/Testing is a live weekly-breakage burden; the Workspace→Internal flip (~30d grace) materially de-risks Andreas's Google reliability.
- **F59 — `GOG_KEYRING_PASSWORD` rotation** **[MUST]** — plaintext-exposed, rotation deferred by Peter; should land before Andreas runs unattended.

---

## §B — Standing constraints Andreas must inherit (encode in his docs/guardrails)

**Security / secrets:** F21 (materialise-once key rotation, never trust in-place `.last-good`) · F25 (auth-profiles materialise-once) · F51 (`op` prints secrets — display-immune SHA-12+len checks only) · F52 (`op://` ASCII-only titles; reference by item-id) · F61 (memorySearch embedding cred in per-agent `auth-profiles.json`; provision `openai:default` on the right path) · F38 (bot-token SHA = 46-byte canonical, display-immune).

**Config hygiene:** F17 (staged same-dir edit + atomic `mv` + paired `chmod 600 && chgrp staff`) · F18 (hot-reload vs restart-trigger key classes) · F60/F62 (`.last-good` drifts; immutable timestamped backup is the only trusted rollback) · F14 (chat URL-rewriter mangles dotted names → shell vars / `jq --arg`) · F20 (`agents/main/` is the inheritance template, keep it) · F72 (re-run the F59-safe sanitiser + push after any live config change) · D22 (memorySearch locked to OpenAI text-embedding-3-small) · D2 (ClawHub skill source-review + allowlist before install).

**Sandbox / runtime:** F27 (Colima socket, not Docker Desktop) · F34 (containers run as host 502:20 → workspace `chown openclaw:staff`) · F58 (gateway auto-provisions memory DBs + auth skeletons) · P6-24 (tools in TOOLS.md + bound binary; per-agent sandbox-block drift — CoS owns sandbox-block currency from 6d) · P6-22 (surface tool failures loudly, never silent — also constrains any chat-suppression knob).

**External actuation / launchd:** F45/D20/F67 (gateway launchd + LaunchAgent ops are GUI-session-bound; run from Screen-Sharing for restarts/bootstraps; P6-56 refinement: SSH-OK *while a console session is active*, rejected only in the no-console/FileVault state) · F55 (launchd not cron for scheduled jobs; `cron` deny-listed except for cos/Andreas, P6-49) · F63 (launchd `gui/<uid>` jobs need explicit `EnvironmentVariables`/PATH) · F69 (parse `launchctl` with `| head -3`, never `head -1`) · F46 (calendar I/O via direct Google API, never `gog calendar events list`).

**Posture:** P6-42 (host-browser = empty allowlist + IP-class SSRF filtering, route by injection-trust not site-list; matches Peter's security philosophy) · P6-35 (sandbox→host egress boundary holds via token-gating + browser guard; **verify whether host-VM egress hardening option-B ever shipped** — bundled to 6c.7 but not recorded as applied) · P6-51 (no-unauthorised-test-spend HARD RULE; cost-tiering; dev/test ring-fence) · P6-33 (CoS-first sequencing; Sophie + non-owner Telegram + signature-PNG deferred past 6d).

---

## §C — 6d build-content (this *is* the CoS's job; design in v7, build in 6d)
- **S9 monitoring/automation suite** — (1) Sunday 04:00 auto-update+validate+auto-rollback [subsumes M4]; (2) daily 04:00 workspace git push **with secret-scanning before push**; (3) hourly self-modification watch on SOUL/AGENTS/DECISIONS/TOOLS/HEARTBEAT [S9(3)/M7]; (4)/(6) nightly 90-day session archive + weekly `openclaw backup` to Drive [needs Drive-write OAuth scope]; (5) 2-hourly Sophie/WhatsApp health-check [Month 2+]; (7) morning-window memory_pressure monitoring [relevant once Andreas adds a 3rd heavy heartbeat].
- **D19** — CoS owns maintenance-cadence + scheduled-work tracking; audits/reminds, never auto-executes. _(This is Andreas's core mandate.)_
- **P6-85 — PER-TASK cost tracker (Peter, 2026-07-02) [MUST — CoS-owned]** — Andreas **builds, owns, and iteratively improves** a tracker that attributes Anthropic spend **per task**, so each task's cost is visible and correctable while usage patterns are still being tuned; he decides format/output/cadence. Supersedes/absorbs the [P6-57]#3/[P6-82] spend-visibility carry (the daily token-volume canary — local `usage-cost`=$0, Console-authoritative). Rationale: the [P6-85] steady-state cost fix (heartbeat cadence/context) was diagnosed blind, by inspection; a per-task readout makes future tuning empirical instead.
- **S5** — disaster-recovery dry-run on different hardware + author DR runbook.
- **S7 / S4** — Agent Documentation System (storage, tier framework, shared KB scaffold, 1Password Note cleanup) + ongoing currency of STATE-OF-NATION/RUNBOOK.
- **M7** — security hygiene cycle (hourly CVE watch, daily audit, weekly `npm outdated -g`, monthly key/OAuth/skills/MEMORY review, stolen-device protocol).

---

## §D — Maintenance cadences (Andreas tracks, Peter executes — all standing)
M1 quarterly Python google-* dep upgrade (first ~Aug 2026) · M2 gogcli/Brew upgrades (schema-touching = ceremony) · M3 macOS major updates twice-yearly (never automated) · M4 OpenClaw quarterly eval (pinned **2026.4.22**; 2026.6.6 available — surface before bump, run `browser-dep-repair.sh` after) · M5 `HOMEBREW_NO_AUTO_UPDATE=1` during ceremonies · M6 delete 2 inert 0.13.0 keyring files [NICE] · S10 Soveren.io email 8-month sunset migration (Phase G).

---

## §E — Defer-past-6d (no action until a real consumer exists)
- **P6-32 / SR-2-for-writes** [DEFER] — Google Docs/Sheets **writes** need an owned GCP project + verified OAuth client (gog's published project has those APIs disabled; reads work). SR-2 (ICS-before-calendar-write) is a **standing precondition not yet triggered** — the helper has no calendar-insert verb, so no write has shipped; fires only when write capability is built. _Caveat: only primary calendars ICS-captured; Soveren Team/CS non-primary not yet exported._
- **P6-33 signature-PNG** [DEFER] — build against real document examples, on-demand.
- **P6-44 per-site browser recipes** [DEFER/on-demand] — ~6 real sites (React/Vue date pickers, multi-step forms); build a recipe when an agent actually hits the site.
- **P6-52 Maps + restaurant reservations** [DEFER] — scoped, largely unblocked (P6-42/P6-32); reservation = L1 prepare-and-surface; needs a per-site recipe. Capability session, not pre-6d.
- **P6-46** — long-turn → gateway-timeout → stale session-lock; mitigation = keep deep flows short + gateway restart; `skipWhenBusy` unavailable in 2026.4.22. Watch.
- **P6-37** — in-sandbox Chromium everyday-site failures; mostly absorbed by the host browser; confirm closure.
- **D18 / S3 — Python 3.9.6 EOL → Homebrew ≥3.13** (target Sept–Oct 2026) [NICE→dated] — silent-breakage debt for Python tooling.
- **F31 — sandbox container retention/cleanup policy** [NICE] — adding Andreas grows the container population with no policy.
- **D27 — Colima VM auto-start plist** [NICE] — partly addressed by P6-2 activation; unattended reliability wants VM persistence (the 6c.9 socket-health-check is the nearer-term lever).
- **P6-36 workspace `.cache`/`.config` cruft** [NICE] — one-time delete-and-surface of stale 11-Jun dirs in both workspaces.
- **P6-20 Charlotte avatar** [NICE] — style locked; `/setuserpic` + `set-identity` application pending; Andreas avatar = final 6d step.

---

## Top-8 shortlist (genuinely still-open *work*, priority order)
1. **S11** inter-agent loop-protection design _(reinforced by the 6c.9 sawtooth)_ — design in v7.
2. **S9 suite** — the bulk of what the CoS does; design in v7, build in 6d.
3. **SR-1 / D23** doc-sync mechanism + publish decision — before doc handoff.
4. **F64 + F68 + F70 + D21/D24** — re-enable the descoped launchd jobs (fail-fast + env + `op`-auth).
5. **F44 / Phase G OAuth** — kill the 7-day refresh-token weekly breakage.
6. **S6** architecture-vs-live-schema reconcile — before authoring Andreas's config.
7. **P6-49 → P6-48** — Andreas remit + ARCHITECTURE v7 (the 6c.10 deliverable).
8. **F31 / S5 / D18** — container retention, DR dry-run, Python 3.13 — system-surface debt to retire as Andreas adds load.

_(Items already being cleared in 6c.9 — §A.1 — are omitted from this shortlist.)_
