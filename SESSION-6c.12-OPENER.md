# SESSION 6c.12 — Resume the pair + Capabilities I (Drive/Sheets write · agent email · browser routing) — riding the owned-GCP keystone
_Opener · generated at 6c.11 close (2026-06-27). 6c.11 built + verified the trust-foundation reliability core (durable TASKS.md + done-by-verification, the watchdog/canary redesign) but could **not** exercise it live — heartbeats were paused (cap in place), it was an SSH session (no restart, D20), and OAuth was dead. 6c.12 is the **resume + first-real-test + Capabilities I** session. **Run it from a Screen-Sharing GUI terminal (D20)** — OAuth consent, gateway restart, heartbeat resume and Charlotte's poller all need it. Read `6c11-OAUTH-FLIP-BRIEF.md`, the 6c.11 closure, and `JUDGEMENT-DOCTRINE.md` before acting._

## Stage 0 — Ledger + sanity (no mutating work until ALL-GREEN)
- Deviations currency: **P6-78/79/80 present**; re-anchor the log sha at open.
- Standard sanity: `openclaw health`; pin **2026.4.22** (do NOT bump); both containers `capable-2026-06`; **A-7:** live == last-good == **`d14bb41a3cc4`** (6c.11 made no config change).
- **Cap state (Peter / Console):** is it past the ~1 Jul reset, or has the workspace cap been raised? This is the gate for heartbeat resume + on-tier (Claude) operation + any F26 test. As of 6c.11 it was **still in place** (GPT-5 fallback).
- **Pause/reliability:** `logs/heartbeat.paused` still present (beats correctly off); watchdog enforcing; tail the watchdog + colima-socket-watchdog logs.

## Stage 1 — WS-0 finish: resume the pair (GUI session)
1. **OAuth flip** — execute `6c11-OAUTH-FLIP-BRIEF.md`: Peter picks the fork (recommend owned-GCP **External "In production"** now, Internal later with marie@fedchenkov.com), creates the project + enables APIs (incl. Docs/Sheets → unblocks WS-3), I wire the re-consent (the one GUI browser step) + display-immune verify; durability proof = no `invalid_grant` after 7+ days (watchdog [P6-79] now auto-escalates if it recurs).
2. **Charlotte's frozen offset** — gateway restart re-inits her poller; verify a **real inbound from Peter** reaches her ([F26]).
3. **Heartbeat RESUME** — only after cap lifted **+** OAuth re-authed: `rm logs/heartbeat.paused` **then** `openclaw system heartbeat enable` (that order — else the watchdog re-disables within 15m). Then verify a real `[heartbeat]` fires and the new watchdog alerting behaves on a live beat.
4. **First real test of the 6c.11 reliability core ([F26], cheap):** give Marie a task she must persist to `TASKS.md` and complete with a verified artefact; confirm the proof-of-done loop and that "done" is honest. Single Sonnet/Haiku turn, tight timeout — **explicit Peter auth before any test >~$5 or Opus/looping** ([P6-51]).

## Stage 2 — WS-3 Drive/Sheets write
On the owned GCP project (Docs/Sheets APIs enabled), add a `drive.file` scope + `drive-upload`/sheet-write verbs to `google_helper.py` ([P6-65]/[P6-32]); keep credential-touching code RO-bound at `/opt`; L1 prepare-and-surface for anything external. Exercise end-to-end (a real file lands, read back).

## Stage 3 — WS-4 agent email (`marie@fedchenkov.com`)
Stand up Marie's own send-capable address (Charlotte stays on Soveren to sunset). Sends are L1 prepare-and-surface (draft → approve) until a class graduates. This is also the trigger to graduate OAuth to **Internal** (marie@fedchenkov.com is a Workspace identity).

## Stage 4 — WS-5 browser routing fix
The OTAs blocked despite the no-allowlist spec → the routing/SSRF over-block hypothesis ([P6-71]/closure). Diagnose + fix the over-block so reputable interactive sites work; per-site recipes only on repeated need (defer travel sites to the Travel agent, 6c.15).

## Stage 5 — Verify (F26, strict cost discipline) + Stage 6 — Close
Exercise each capability through a **real but cheap** agent turn (Sonnet/Haiku never Opus; single; tight timeout; inspection-first). A capability is live only on a real delivered artefact. Close: deviations additive; `SESSION-6c.12-CLOSURE.md`; `SESSION-6c.13-OPENER.md` (Capabilities II — payments/Revolut + accounts/1Password, high-trust). Re-anchor shas; commit `_session-docs/`.

## Safety rails (whole session)
Reversible build runs without prompting; **surface irreversible/destructive:** OAuth/Workspace/GCP/billing, the cap, version-pin/D1, payments, depairing, reboots, destructive migrations. **Cost ([P6-51]):** inspection-first; F26 turns single/short/Sonnet/tight; explicit auth before >~$5 or Opus/looping/browser-heavy. **Secrets ([F21/F51]/[P6-66]):** never read secret values; redact by value-shape; the owned-client JSON is referenced by path only. GUI/Screen-Sharing for restart/consent/LaunchAgent ops (D20). Stop at ~63% context for a clean close.

## Carry-forward (deferred, not lost)
Per-turn tool/token **ceiling** (runtime) + full **Telegram back-scroll** (runtime) → fold into this session's runtime work or 6c.13; **payments + accounts** → 6c.13; **acceptance gate** → 6c.14; **Travel agent** → 6c.15; **CAPSTONE ARCHITECTURE v7 + reconciliation** → 6c.16 (immediately before 6d Andreas — NOT before the acceptance gate, [P6-76]). Standing: [F59]/[P6-66] rotations (deferred), S6 reconcile, SR-1/D23, F64/F68/F70 launchd, 2026.4.22 pinned; model+framework currency ([P6-74]) owned by Andreas, pin-bump surfaced to Peter; reconcile Marie's "no reliable calendar access" memory line at OAuth-restore.
