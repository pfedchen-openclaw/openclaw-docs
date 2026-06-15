# SESSION 6c.10 — Confirm-the-fixes + canonical reconciliation + ARCHITECTURE v7
_Opener · generated at 6c.9 close (2026-06-15). 6c.9 was meant to be housekeeping; it ended up finding that **the agents had never been able to ping Peter** ([P6-64], heartbeat `target` defaulted to `"none"`) and fixing it — delivery proven via direct send, but the **autonomous beat** delivering a digest was not yet observed when Peter chose to finalise. So 6c.10 opens with **confirmation duty first**, then proceeds to the originally-planned milestone: canonical reconciliation + ARCHITECTURE v7 (the Andreas-ready spec). Read `PRE-COS-BACKLOG.md` and the 6c.9 closure before acting._

## Stage 0 — Ledger + sanity (no mutating work until ALL-GREEN)
- Deviations log currency: confirm **P6-61…P6-66 present**; P6-57 RESOLVED, P6-59 committed, P6-60 wiring-surfaced. Re-anchor the log sha (recorded at 6c.9 commit).
- Standard sanity: `openclaw health` ok; `18789/healthz`→200; pin **2026.4.22** (2026.6.6 available — do NOT bump); both containers `capable-2026-06`; op SERVICE_ACCOUNT; pa/pro interactive `opus-4-7`, heartbeat `sonnet-4-6`.
- **A-7:** live==last-good == **`d14bb41a3cc4`** (the 6c.9 heartbeat-target change). If they differ, investigate before any edit.
- **Reliability:** `tail logs/heartbeat-watchdog.log` (continuous `ok … (heartbeat-last)`, no sawtooth); `tail logs/colima-socket-watchdog.log` (`ok docker socket reachable`); both LaunchAgents loaded (`launchctl print gui/502/…`).

## Stage 1 — CONFIRM the 6c.9 fixes are real end-to-end ([P6-64] lead)
**This is the gate. Do it first, by inspection (zero spend).**
- **Autonomous ping delivery:** since 6c.9, did a real *beat* deliver to Peter? Check `openclaw system heartbeat last --json` for `status:"sent"` / `silent:false`; grep `gateway.log` for `using explicit accountId … channel=telegram` and any `status:sent`/`skipped reason:…`; confirm Peter received Marie's 08:00 digest + any surfacing. **If still silent on substantive beats:** the residual gates are `channels.telegram.accounts.pa.heartbeat.showOk` (default false — fine for quiet beats) and the `dmPolicy:"pairing"` outbound gate / `directPolicy` (must be `allow`); and `showAlerts` (default true). Walk [P6-64] in the log. Do **not** declare it fixed without a real delivered beat.
- **Comms-hygiene:** spot-check that the next real digest obeys (b) no-sign-off + (c) no technical leakage (the [P6-62] persona-runtime exercise that rode the next beat).
- **Drive:** confirm Marie now *surfaces* the no-upload limit rather than failing opaque ([P6-65]); `shared-docs/` in use.

## Stage 2 — [P6-60] wire the key zones (the carried build-step-1)
- `pa`→zone1 (no-op, done). Wire **`pro`→zone2** + route Claude-Code/test turns to **zone4**. Method (F17/F21): read each key from 1Password → `jq` into the per-agent `auth-profiles.json` **by structure** (never print the value) → display-immune sha-12/len verify against 1Password → atomic 0600/staff → A-7 re-anchor → **one cheap pro turn** to confirm zone2 resolves. zone3 stays dormant→Andreas at 6d.

## Stage 3 — Canonical reconciliation + ARCHITECTURE v7 (the designed milestone, [P6-48]/[P6-49])
- Fold `PRE-COS-BACKLOG.md` into a reconciled **ARCHITECTURE v7** = the Andreas-ready CoS spec. Must include: the **engineering-steward remit** (PROPOSE-not-EXECUTE, respects the version pin, owns the SR-1 doc autopilot, knows the P6-40/42 update-wipe); **S11 inter-agent loop-protection design** (highest-risk gap — the [P6-59] sawtooth is the cautionary tale: a supervisor must not be able to harm what it watches); the **proactive-delivery contract** ([P6-64] — every agent→human capability is "live" only on a real delivered artefact, never a composed one); SR-1/D23 doc-sync + publish decision; reconcile MASTER-ARCH Part 5 vs the live 2026.4.22 schema (S6) **before** authoring Andreas's config.
- Reconcile STATE-OF-NATION / RUNBOOK / ARCHITECTURE from the 6c deviation deltas (the milestone-boundary doc-tier sync).

## Stage 4 — Close (three deliverables)
- DEVIATIONS-LOG additive; SESSION-6c.10-CLOSURE.md; SESSION-6d-OPENER.md (Andreas build kickoff) **iff** Stage-1 confirms + the backlog's must-fix-before-6d items are genuinely cleared — else a 6c.11 to finish them. Re-anchor shas; commit `_session-docs/` + the F72 sanitised snapshot.

## Safety rails (whole session)
Reversible build runs without prompting; surface irreversible/destructive (real external sends beyond owner-pings, payments, **version-pin/D1, billing/cap/workspace changes incl. key-zone wiring verification, reboots**, depairing, destructive migrations). **Cost ([P6-51] hard rule):** billed `openclaw agent` turns spend Peter's money — default to inspection; any F26 turn = single, short, **Sonnet/Haiku never Opus**, tight timeout, no loops/batches; explicit authorisation before testing >~$5 or Opus/looping/browser-heavy/batched. **Secrets ([F21/F51], reinforced by [P6-66]):** never read secret values; redact config dumps by value-shape / descend-and-redact, never a top-level key filter. `openclaw config set` is the gate-clear config path (auto-validates, handles the secret-bearing sections). Gateway restart/LaunchAgent bootstrap SSH-safe while a console session is active ([P6-56]). Stop at ~63% context for a clean close.

## Carry-forward (deferred, not lost)
[F44]/Phase-G OAuth flip (+ Drive-write done properly) · [P6-32] Docs/Sheets writes · SR-2 ICS-before-calendar · [P6-33] signature-PNG · F64/F68/F70/D21-D24 launchd jobs · [P6-36] workspace cruft · [P6-20] Charlotte avatar · [P6-52] Maps/reservations · [F59]+[P6-66] token rotations (Peter deferred) · `agent:pa:main` 80%-context reset ([P6-46]) · 2026.6.6 update (pinned — surface before bump, run `browser-dep-repair.sh` after).
