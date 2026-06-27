# SESSION 6c.13 — Capabilities II: payments (Revolut, gate-at-card) + accounts (1Password) — high-trust; plus WS-4 agent email (GUI)
_Opener · generated at 6c.12 close (2026-06-27). 6c.12 shipped **Drive/Docs/Sheets write** (F26 PASS, [P6-83]) and diagnosed the **browser over-block away** ([P6-84] — already in the no-allowlist posture Peter wants); WS-4 agent email was correctly carried (GUI/Workspace-gated). 6c.13 is the **high-trust** capabilities session: the **payment prepare-and-surface flow** and the **1Password account-access pattern**, both heavily surface-gated because real payments are irreversible. Read the 6c.12 closure, `JUDGEMENT-DOCTRINE.md`, and `6c11-OAUTH-FLIP-BRIEF.md` (for WS-4) before acting._

## Run this in a Screen-Sharing GUI terminal (D20)
WS-4 (Workspace + Internal OAuth consent) and any interactive `op`/1Password auth (F70) are **GUI-bound**. 6c.12 was SSH-only and had to carry WS-4 — don't repeat that. Open from a Terminal inside Screen Sharing so the restart/consent/`op signin` paths are available.

## Stage 0 — Ledger + sanity (no mutating work until ALL-GREEN)
- Deviations currency: **P6-83/84 present**; re-anchor the log sha at open.
- Standard sanity: `openclaw health`; pin **2026.4.22** (do NOT bump — `status` will keep offering 2026.6.10); both containers `capable-2026-06`; **A-7:** live == last-good == **`8823761e1900`/9994B** (6c.12 made NO config change — verify, don't trust this line blindly: 6c.12's own opener carried a stale A-7 anchor, [P6-83]). Heartbeats enabled, beats on `claude-opus-4-7`; OAuth alive (a `google_helper` read works — watch the [F44] 7-day mark, though Production status holds); watchdog + colima-socket-watchdog clean.
- **Confirm 6c.12 held:** a `google_helper.py doc-create`/`drive-list` still works (the RO bind is intact); `browser.ssrfPolicy.hostnameAllowlist` still `[]` (if anything repopulated it, [P6-41] over-block could return — [P6-84]).

## Stage 1 — Payments: the Revolut prepare-and-surface flow (gate value at the card, NOT the agent)
Design constraint [P6-45] + Peter's philosophy: **the spend ceiling lives at the card issuer, not in agent logic.** Build/confirm the L1 flow in the PA-PLAYBOOK §Payments: the agent assembles a complete, checkable payment proposal (payee, amount+currency, what-for, the exact card) and **surfaces it** — Peter executes or approves; the card itself is never auto-entered in v1. The "Marie PA" card lives canonically in **1Password** (vault "OpenClaw"); v1 does NOT provision it into `auth-profiles.json` (that's the L3+ auto-pay step, gated separately). **Real payment = irreversible → SURFACE, never auto-execute, even on a clean proposal.** Verify by inspection + a single dry proposal turn; NEVER make a live payment as a test.

## Stage 2 — Accounts: the 1Password access pattern (F70-aware)
Stand up how an agent retrieves an account credential when needed: `op` CLI against vault "OpenClaw" (EU shard my.1password.eu), `op read --no-newline | sha256sum` for display-immune verify. Known blocker [F70]: `op` is unauthenticated under launchd (no `OP_SESSION`) — so unattended retrieval needs a service-account/token pattern, not interactive `op signin`. Decide + wire the minimal-privilege path; secrets never land in workspace files; display-immune checks only (F21/F51).

## Stage 3 — WS-4 agent email `marie@fedchenkov.com` (the carry — GUI)
Stand up Marie's send-capable identity (Charlotte stays on Soveren to sunset). `gmail.modify` is already granted (send-capable), so once the fedchenkov.com Workspace mailbox + the Internal-OAuth graduation land (per `6c11-OAUTH-FLIP-BRIEF.md` Path B), wiring is small. Sends are L1 prepare-and-surface (draft→approve) until a class graduates.

## Stage 5 — Verify (F26, strict cost) + Stage 6 — Close
Exercise each capability through a **real but cheap** turn (single; tight; the real gateway path, not embedded-CLI — [P6-81]); a capability is live only on a delivered artefact (a surfaced proposal counts for payments — do NOT live-pay to test). Close: deviations additive; `SESSION-6c.13-CLOSURE.md`; `SESSION-6c.14-OPENER.md` (the **acceptance gate** — [P6-76]). Re-anchor shas; commit `_session-docs/`.

## Safety rails (whole session)
Reversible build runs without prompting; **surface irreversible/destructive:** real payments, OAuth/Workspace/GCP/billing, the cap, version-pin/D1, 1Password writes/deletes, depairing, reboots, destructive migrations. **Cost ([P6-51]):** inspection-first; F26 turns single/short/tight; explicit auth before >~$5 or Opus-looping/browser-heavy/batched. **Secrets ([F21/F51]/[P6-66]):** never read secret values; the Revolut card + `op` creds are referenced by structure only; helper code in `agent-creds/` (deny-Read) is edited by copy-roundtrip ([P6-83]). GUI/Screen-Sharing for restart/consent/`op`/LaunchAgent ops (D20). Stop at ~63% context for a clean close.

## Carry-forward (deferred, not lost)
Per-turn tool/token **ceiling** (runtime — the [P6-79] canary is the detective stand-in) + full **Telegram back-scroll** (runtime — [P6-78]) → fold into 6c.13 runtime work or 6c.14; **F64/F68/F70 launchd** re-enable (tied to the Stage-2 `op`-auth work); **acceptance gate** → 6c.14; **Travel agent** (+ per-site browser recipes) → 6c.15; **CAPSTONE ARCHITECTURE v7 + reconciliation** → 6c.16 (immediately before 6d Andreas, NOT before the acceptance gate — [P6-76]). Standing: [F59]/[P6-66] rotations (deferred), S6 reconcile, SR-1/D23, 2026.4.22 pinned; model+framework currency ([P6-74]) owned by Andreas; the "6c12 write test" Drive doc can be trashed.
