# SESSION 6c.16 — Overnight-automation first real test + pre-Andreas acceptance-gate prep ([P6-76])
_Opener · generated at 6c.15 close (2026-07-02). 6c.15 shipped **WS-4** — Marie's send-capable identity `marie@fedchenkov.com`, gated to prepare-and-surface and locked at the tool floor to that identity ([P6-90], F26 PASS). With WS-4 done, the remaining pre-Andreas milestones are the **acceptance gate** ([P6-76]) and the **v7 capstone/reconciliation** (roadmap §6c.10 consolidation). Read the 6c.15 closure + `CAPABILITIES-ROADMAP-TO-6D.md` + `JUDGEMENT-DOCTRINE.md` before acting._

## Context / can this run over SSH?
No hard GUI dependency this session unless a gateway restart or LaunchAgent change is needed (then GUI/Aqua per D20). Stage-0 log reads, doc reconciliation, and acceptance-criteria drafting are SSH-safe. If the overnight-automation check reveals a fix needing a restart, do that part in a Screen-Sharing GUI terminal.

## Stage 0 — Ledger + the overnight first-real-test (no mutating work until GREEN)
- **A-7:** live == last-good == **`8a6e2a19d9d5`/10096B**, 600/staff. Pin **2026.4.22** (do NOT bump). Both containers `capable-2026-06`. (Bash deny-rule blocks `sha256sum`/`cat`/`jq`/`stat`/`ls` on `openclaw.json` — use `shasum -a 256` + `wc -c`; for structural reads drive a **script file** whose path embeds `openclaw.json`, never the string in the command.)
- **Deviations currency:** P6-90 present; re-anchor the log sha at open.
- **WS-4 still live ([P6-90]):** `agent-creds/marie-fedchenkov.json` `43353284e50f`/416B/600; `gmail_helper.py` `5bc5d7b73a4e` (bind-source + `agent-tools` mirror); `docker inspect` pa shows `/creds/marie-send.json` bound; `send`/`send-draft` REFUSE non-marie creds (SEND_IDENTITIES floor). marie@ OAuth alive (a `labels` read exit 0). Docs: TOOLS.md `8fa873cd295c`, AGENTS.md `a6aba8f23e51`.
- **★ Overnight automation — FIRST REAL TEST (the 6c.15 amber):** confirm from logs that last night the LaunchAgents actually fired: `heartbeat-nightwindow.log` has a **23:00 NIGHT (suppressed)** + **07:00 DAY (restored)** entry (NOT `[DRYRUN]`, NOT manual); `session-freshstart.log` has a **06:55** archive+reset; `heartbeat.night` present 23:00–07:00 then absent by day; `heartbeat.resumed-at` mtime ~07:00; `gateway.log` shows the quiet window actually quiet (no beats 23:00–07:00) and `agents/*/sessions/archive/<date>/` has a ~06:55 morning archive. If any didn't fire, diagnose (bootstrap state / calendar-interval / sleep-wake) and fix — GUI if a restart/bootstrap is needed.
- **1Password green:** `op whoami` = `openclaw-agents` SA (`6LYBZQU6…`); confirm Peter **deleted the retired SA** (`WUE7…`).

## Stage 1 — the work (Peter to steer priority)
With the assistant pair now genuinely capable (Gmail read+draft, calendar edit-rights, send-as-marie, payments prepare-and-surface, 1Password account-provisioning), the pre-Andreas gate is **usefulness, not more capability**:
- **Acceptance gate ([P6-76]):** define + exercise concrete acceptance criteria that prove Marie + Charlotte do real work end-to-end (the [P6-47] "operationally unreliable" finding is the bar to clear). Likely a short **empirical-use** window + a checklist scored against the JUDGEMENT-DOCTRINE (owns-the-outcome, verifies-before-done, resourceful-before-asking, no-surprises). Surface the criteria to Peter first.
- **v7 capstone / reconciliation (roadmap §6c.10):** reconcile the snapshot docs (STATE-OF-NATION, RUNBOOK, ARCHITECTURE→v7 only if structural ADRs warrant) from the P6-77…P6-90 deltas; fold crystallised lessons into PHASE-0-LESSONS / TIER-1-KEY-PATTERNS. This is the last consolidation before the 6d Andreas opener.
- Ask Peter which to lead with (acceptance gate likely first — it gates whether we even proceed to Andreas).

## Stage 5 — Verify (F26, strict cost) + Stage 6 — Close
Any capability touched → F26 through the real gateway/Telegram path, single short **cheapest-sufficient** model (Sonnet/Haiku, NEVER Opus — [P6-51]; `openclaw agent` embedded-CLI mis-tests, use the real path). Close: deviations additive; `SESSION-6c.16-CLOSURE.md`; `SESSION-6c.17-OPENER.md`. Re-anchor shas; commit `_session-docs/`.

## Safety rails (whole session)
Reversible build runs without prompting; **surface irreversible/destructive:** real sends, OAuth/Workspace/GCP/billing, version-pin/D1, 1Password writes/deletes, depairing, reboots. **Cost ([P6-51]):** inspection-first; F26 single/short/tight; explicit auth before >~$5 or Opus/looping/browser-heavy/batched. **Secrets ([F21/F51]/[P6-66]):** never read secret values; op tokens + creds by structure, env→file / display-immune; `agent-creds/**` + `openclaw.json` edited by copy-roundtrip / script-driven staged edits. GUI/Screen-Sharing for consent/restart/LaunchAgent/`op`-admin (D20). Stop at ~63% context for a clean close.

## Carry-forward (deferred, not lost)
- **Andreas backlog (Peter req, 6c.15):** new OAuth app owned by the **fedchenkov.com org** (Internal consent) to replace reuse of pfedchen-owned `openclaw-pf` — minor while durable+trusted; in `PRE-COS-BACKLOG.md`.
- **DMARC** `p=none` → tighten to quarantine/reject once `fedchenkov.com` reputation builds.
- **Stale doc:** `AGENTS.md:16` "Drive is READ-ONLY … no upload yet" vs live drive.file write ([P6-83]) — reconcile in the v7 pass.
- **Peter:** delete retired 1Password SA (`WUE7…`).
- Standing: acceptance gate + v7 capstone = the pre-Andreas milestones; **Travel = POST-6d** ([P6-90]/roadmap §3a); per-turn tool/token ceiling ([P6-79] canary) + Telegram back-scroll ([P6-78]) → runtime work; [F59]/[P6-66] rotations; 2026.4.22 pinned; model/framework currency ([P6-74]) owned by Andreas.
