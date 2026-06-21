# SESSION 6c.11 — Trust foundation: unblock + reliability + the judgement doctrine (make Marie & Charlotte trustworthy before new capability)
_Opener · generated at 6c.10 close (2026-06-20). 6c.10 diagnosed both assistants as unusable and locked a remediation programme ([P6-67…P6-73]) + a judgement doctrine (`JUDGEMENT-DOCTRINE.md`). 6c.11 executes the **trust foundation** — restore basic function, make tasks un-loseable and "done" honest, apply the doctrine, add cost controls + proper alerting. **New capabilities (Drive-write, email, payments, accounts) are 6c.12–13; the acceptance gate is 6c.14. Do NOT touch ARCHITECTURE v7 / Andreas.** Read `JUDGEMENT-DOCTRINE.md`, `6c11-BRIEF-EDITS-STAGED.md`, and the 6c.10 closure before acting._

## Stage 0 — Ledger + sanity (no mutating work until ALL-GREEN)
- Deviations currency: **P6-67…P6-73 present**; re-anchor the log sha at this session's open.
- Standard sanity: `openclaw health`; pin **2026.4.22** (do NOT bump); both containers `capable-2026-06`; pa/pro interactive `opus-4-7`, heartbeat `sonnet-4-6`; **A-7:** live == last-good == **`d14bb41a3cc4`**.
- **Cap/fallback state (Peter):** is the Anthropic Console cap raised, or are we past 2026-07-01 (cap reset)? Confirm GPT-5 fallback still in place. Until the cap is lifted, interactive turns run on GPT-5 — factor into any F26 test.
- **Reliability:** `tail` the heartbeat- and colima-socket-watchdog logs; both LaunchAgents loaded.

## Stage 1 — WS-0 Unblock (restore basic function)
- **Google OAuth (both agents):** re-auth now; then the **[F44] Internal-OAuth flip** on an **owned GCP project** to end the 7-day token death (this keystone also unblocks 6c.12 Drive-write + agent email). **Surface as a decision — billing/Workspace/GCP = Peter's action.**
- **Charlotte's frozen Telegram receive-offset** (stuck since 17 Jun; the [P6-64] fix was `pa`-only) — reset for `pro`; verify by a real inbound from Peter reaching her.
- **Watchdog storm** — fix the fallback-alert dedup so it stops paging every ~15 min, *without* losing genuine-blocker escalation (folds into WS-8).
- **Console cap** — Peter's action; surface, don't attempt.

## Stage 2 — WS-1 Reliability core (never lose a task; never fake "done")
- **Durable `TASKS.md`** — written on accept ("the write is the acknowledgement"), read every heartbeat, fires on overdue/committed items.
- **Done-by-verification (F26-for-tasks)** — a task is done only when the artefact is confirmed (row read back, mail in Sent), never on the agent's say-so.
- **Full Telegram-history access** — a tool to query the real back-scroll (the "check earlier messages" gap).
- **Context/memory hygiene** — MEMORY.md tiering within the bootstrap budget; session compaction; right-size the heartbeat cadence; enforce daily-log capture (the empty 18–20 Jun files).

## Stage 3 — WS-2 Judgement (edits APPLIED at 6c.10 close — VERIFY + sweep, don't re-apply)
The staged edits were applied live to both agents at 6c.10 close (Peter's instruction) — doctrine + audit fixes + natural-cadence + autonomy-overlay ([P6-75]). This stage is now: **verify them on a real beat** + run a **memory-hygiene sweep** for other stale/contradictory lines (one stale payment line already fixed). For the record, what was applied (`6c11-BRIEF-EDITS-STAGED.md`): the doctrine into Marie's SOUL/AGENTS/PLAYBOOK/MEMORY, mirrored to Charlotte, **plus the [P6-70] audit fixes** — delete the Drive contradiction; replace the harmful "Peter does the checkout himself" memory with end-to-end ownership; add the missing competence rules (availability, currency-in-quotes, forms-default, hold-morning, check-sources); install the durable-vs-situational protocol + demote the "prefer new" note; reconcile the Olga line; Charlotte's IDENTITY DM carve-out + timezone discipline.

## Stage 4 — WS-8 Alerting + WS-9 Cost controls
- **Watchdog redesign** — per-agent health + per-task failure-loop detection; deduplicated/throttled (escalate, don't repeat every 15 min); **guaranteed real-blocker escalation as a new outbound message** (so a dead inbox/OAuth reaches Peter); both agents.
- **Cost controls** — per-turn and per-task tool-call + token/cost ceilings with abort-and-surface on a doomed flow; heartbeat right-sizing; a token-volume spend canary feeding the alerting.

## Stage 5 — Verify (F26, under strict cost discipline)
Exercise the doctrine + task-persistence + alerting through **real but cheap** agent turns: single, **Sonnet/Haiku never Opus**, tight timeout, no loops/batches; inspection-first wherever possible (the whole crisis was test/operating spend). **Explicit Peter authorisation before any test estimated >~$5.** A capability is live only on a real delivered artefact.

## Stage 6 — Close
Deviations additive; `SESSION-6c.11-CLOSURE.md`; `SESSION-6c.12-OPENER.md` (Capabilities I — Drive/Sheets write, agent email, browser routing fix). Re-anchor shas; commit `_session-docs/`.

## Safety rails (whole session)
Reversible build runs without prompting; **surface irreversible/destructive**: OAuth/Workspace/GCP/billing changes, the Console cap, version-pin/D1, payments, depairing, reboots, destructive migrations. **Cost ([P6-51] hard rule):** billed `openclaw agent` turns spend Peter's money — inspection-first; F26 turns single/short/Sonnet/tight; explicit auth before >~$5 or Opus/looping/browser-heavy. **Secrets ([F21/F51], [P6-66]):** never read secret values; redact by value-shape. Gateway restart / LaunchAgent ops from a **Screen-Sharing GUI terminal** (D20/P6-56). Stop at ~63% context for a clean close.

## Carry-forward (deferred, not lost)
Capabilities (Drive/Sheets write, agent email, browser robustness) → **6c.12**; payments + accounts + credential handling → **6c.13**; family channels (Olga/parents, WS-11) → with the messenger build; **acceptance gate** → **6c.14**; **Travel agent before Andreas**; ARCHITECTURE v7/Andreas gated behind the acceptance gate. Standing: [F59]/[P6-66] rotations (deferred), S6 reconcile, SR-1/D23, F64/F68/F70 launchd, 2026.6.6 (pinned); **model+framework currency** ([P6-74]) — Opus-4.8 / newer-framework upgrade owned by Andreas, pin-bump surfaced to Peter.
