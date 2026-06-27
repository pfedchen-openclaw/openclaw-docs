# SESSION 6c.12 — Resume the pair + Capabilities I (Drive/Sheets write · agent email · browser routing) — riding the owned-GCP keystone
_Opener · generated at 6c.11 close (2026-06-27), updated at the 6c.11 live completion ([P6-81]). **6c.11 finished LIVE:** OAuth durably fixed (Peter's `openclaw-pf` project published to Production), Charlotte's poller unfrozen, the Anthropic cap raised $500→$700 with Claude confirmed on the real path, the reliability core F26-passed, and **heartbeats resumed** — the pair is back, trustworthy, and verified. So 6c.12 is a clean **Capabilities I** session (Drive/Sheets write · agent email · browser routing), riding the now-owned `openclaw-pf` project. Read the 6c.11 closure, `6c11-OAUTH-FLIP-BRIEF.md`, and `JUDGEMENT-DOCTRINE.md` before acting._

## Stage 0 — Ledger + sanity (no mutating work until ALL-GREEN)
- Deviations currency: **P6-78/79/80/81 present**; re-anchor the log sha at open.
- Standard sanity: `openclaw health`; pin **2026.4.22** (do NOT bump); both containers `capable-2026-06`; **A-7:** live == last-good == **`d14bb41a3cc4`** (6c.11 made no config change — all work was workspace files + scripts + Peter's GCP console).
- **Confirm the resumed pair is healthy:** heartbeats enabled (`logs/heartbeat.paused` absent); recent beats resolving to `claude-opus-4-7` (not gpt-5) in `gateway.log`; OAuth still alive (a `google_helper` read works — and watch for the [F44] 7-day mark, though Production status should hold; [P6-79] watchdog auto-escalates an `invalid_grant`); watchdog + colima-socket-watchdog logs clean.
- **Cap headroom:** the $700 cap resets monthly; Marie+Charlotte guidance ~$300–400/mo ([6c.7]). Watch the [P6-79] spend/context canary.

## Stage 1 — WS-3 Drive/Sheets write (the natural first capability — already half-unblocked)
6c.11 pre-cleared the blockers: `openclaw-pf` now has Drive/Docs/Sheets/Slides APIs enabled and the agents already hold the **`drive.file`** write scope (granted in the 6c.11 re-consent). So: add `drive-upload` / sheet-write / doc-write verbs to `google_helper.py` ([P6-65]/[P6-32]); keep credential-touching code RO-bound at `/opt`; L1 prepare-and-surface for anything external. Exercise end-to-end through the **real gateway path** (a real file lands in Drive, read back) — **not** the embedded `openclaw agent` CLI, which mis-tests ([P6-81]).

## Stage 2 — WS-4 agent email (`marie@fedchenkov.com`)
Stand up Marie's own send-capable address (Charlotte stays on Soveren to sunset). Sends are L1 prepare-and-surface (draft → approve) until a class graduates. This is also the trigger to graduate OAuth to **Internal** (marie@fedchenkov.com is a Workspace identity).

## Stage 3 — WS-5 browser routing fix
The OTAs blocked despite the no-allowlist spec → the routing/SSRF over-block hypothesis ([P6-71]/closure). Diagnose + fix the over-block so reputable interactive sites work; per-site recipes only on repeated need (defer travel sites to the Travel agent, 6c.15).

## Stage 5 — Verify (F26, strict cost discipline) + Stage 6 — Close
Exercise each capability through a **real but cheap** agent turn (Sonnet/Haiku never Opus; single; tight timeout; inspection-first). A capability is live only on a real delivered artefact. Close: deviations additive; `SESSION-6c.12-CLOSURE.md`; `SESSION-6c.13-OPENER.md` (Capabilities II — payments/Revolut + accounts/1Password, high-trust). Re-anchor shas; commit `_session-docs/`.

## Safety rails (whole session)
Reversible build runs without prompting; **surface irreversible/destructive:** OAuth/Workspace/GCP/billing, the cap, version-pin/D1, payments, depairing, reboots, destructive migrations. **Cost ([P6-51]):** inspection-first; F26 turns single/short/Sonnet/tight; explicit auth before >~$5 or Opus/looping/browser-heavy. **Secrets ([F21/F51]/[P6-66]):** never read secret values; redact by value-shape; the owned-client JSON is referenced by path only. GUI/Screen-Sharing for restart/consent/LaunchAgent ops (D20). Stop at ~63% context for a clean close.

## Carry-forward (deferred, not lost)
Per-turn tool/token **ceiling** (runtime) + full **Telegram back-scroll** (runtime) → fold into this session's runtime work or 6c.13; **payments + accounts** → 6c.13; **acceptance gate** → 6c.14; **Travel agent** → 6c.15; **CAPSTONE ARCHITECTURE v7 + reconciliation** → 6c.16 (immediately before 6d Andreas — NOT before the acceptance gate, [P6-76]). Standing: [F59]/[P6-66] rotations (deferred), S6 reconcile, SR-1/D23, F64/F68/F70 launchd, 2026.4.22 pinned; model+framework currency ([P6-74]) owned by Andreas, pin-bump surfaced to Peter; reconcile Marie's "no reliable calendar access" memory line at OAuth-restore.
