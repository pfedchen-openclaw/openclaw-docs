# SESSION 6c.15 — WS-4 agent email `marie@fedchenkov.com` (the deferred headline) + acceptance-gate prep
_Opener · generated at 6c.14 close (2026-07-02). 6c.14 was an SSH session: it shipped Marie's calendar **edit-rights** end-to-end ([P6-89] — `calendar-insert`/`calendar-update`/`calendar-cancel`, act-directly for her own solo events, gated cancel, F26-confirmed by Peter) and deferred WS-4, which is GUI/browser-bound. 6c.15 is WS-4's focused run. Read the 6c.14 closure, `6c11-OAUTH-FLIP-BRIEF.md` (Phase-5 Path B), and `JUDGEMENT-DOCTRINE.md` before acting._

## Run this in a Screen-Sharing GUI terminal (D20)
Phase 5 (Internal-OAuth graduation + consent) and any gateway restart are GUI-bound. **Verify context at open:** `launchctl managername` should be `Aqua` (not SSH). **Exception:** WS-4 Phases 1–4 (Workspace signup + Cloudflare DNS + user creation) are pure browser + Cloudflare — Peter can do them from anywhere ahead of the GUI session; only Phase 5 needs the Mini/GUI.

## Stage 0 — Ledger + sanity (no mutating work until ALL-GREEN)
- Deviations currency: **P6-86/87/88/89 present**; re-anchor the log sha at open.
- A-7: live == last-good == **`a8783dcd17dc`/9993B**, 600/staff. Pin **2026.4.22** (do NOT bump). Both containers `capable-2026-06`. (Note: a Bash deny-rule blocks `sha256sum openclaw.json` — use `shasum -a 256` + byte length for the A-7 check.)
- **google_helper calendar edit-rights ([P6-89]) still live:** `agent-creds/google_helper.py` = **`46a9243f954a`/24083B** (644); `docker exec` each container → bound `/opt/google_helper.py --help` lists `calendar-insert`/`calendar-update`/`calendar-cancel`. Marie docs at TOOLS.md `09149235201d`, PA-PLAYBOOK.md `cfe9c034b2cf`.
- **Deferred from 6c.14 (run now):** confirm 6c.13's cost-hardening held over the cycle — (a) `gateway.log` beats at **2h**, overnight quiet window 23:00–07:00; (b) **06:55 fresh-session** fired (`agents/*/sessions/archive/<date>/` has morning archives; `:main` lean ~50K, contextTokens=120000); (c) watchdog clean through the night (night-sentinel skip; no restart-loop); (d) `heartbeat.night` absent by day, `heartbeat.resumed-at` ~07:00.
- **1Password green:** `OP_SERVICE_ACCOUNT_TOKEN` sources from the 0600 file (new terminal); `op whoami` = `openclaw-agents` SA (Integration `6LYBZQU…`); `op-get.sh --verify` works. Confirm Peter **deleted the retired SA** (`WUE7…`).
- OAuth alive (a `google_helper` calendar/drive read exit 0 — [F44] durable via the owned project).

## Stage 1 — WS-4 agent email (guided live)
Stand up Marie's send-capable identity (`marie@fedchenkov.com`); Charlotte stays on Soveren to the sunset. `gmail.modify` is already granted (send-capable).

**Decisions locked (Peter, 6c.12):** `peter@fedchenkov.com` super-admin + `marie@fedchenkov.com` standard user (2 seats, clean separation); registrar = Cloudflare; plan = Business Starter (~$7/user/mo, 14-day trial — verify live price); every created password → 1Password (Agent-Provisioned or a personal vault, Peter's call — NOT OpenClaw ops).

**Phases 1–4 — Peter's browser + Cloudflare (no Mini/GUI; Claude guides live):**
1. Workspace + admin — workspace.google.com → Start free trial → "Yes, I have a domain" (never let Google *register* one) → `fedchenkov.com` → create admin `peter@fedchenkov.com` (strong pw → 1Password) → Business Starter trial.
2. Verify domain — Google's `google-site-verification=…` TXT → Cloudflare (DNS → Add TXT, name `@`, DNS only) → Verify.
3. Route email (MX) — add Google's MX at Cloudflare (current new-setup = single `smtp.google.com` priority 1; MX = DNS only; remove conflicting MX).
4. Create the user — admin.google.com → Users → Add `marie@fedchenkov.com` (**standard user, NOT admin**; strong pw → 1Password) → test email both ways.

**Phase 5 — Claude's OpenClaw wiring (GUI, AFTER the mailbox exists):**
5. Graduate OAuth to **Internal** on a GCP project inside the fedchenkov.com org (move/own `openclaw-pf` or a new in-org project) per `6c11-OAUTH-FLIP-BRIEF.md` Path B → run the consent for `marie@fedchenkov.com` (GUI/D20) → mint a durable cred JSON → bind for Marie (helper reads `/creds/google.json`; **decide identity *swap* vs *second mailbox*** — surface to Peter) → add a **send** verb to `gmail_helper.py` (gmail.modify already covers send). Sends stay **L1 prepare-and-surface** (draft→approve) until a class graduates.

## Stage 5 — Verify (F26, strict cost) + Stage 6 — Close
F26: a real **prepared+approved send** from `marie@fedchenkov.com` through the actual gateway path (single, short, cheapest sufficient model — [P6-51]/[P6-81]). Close: deviations additive; `SESSION-6c.15-CLOSURE.md`; `SESSION-6c.16-OPENER.md`. Re-anchor shas; commit `_session-docs/`.

## Safety rails (whole session)
Reversible build runs without prompting; **surface irreversible/destructive:** real sends, OAuth/Workspace/GCP/billing, version-pin/D1, 1Password writes/deletes, depairing, reboots. **Cost ([P6-51]):** inspection-first; F26 single/short/tight; explicit auth before >~$5 or Opus-looping/browser-heavy/batched. **Secrets ([F21/F51]/[P6-66]):** never read secret values; op tokens + creds referenced by structure, handled env→file / display-immune; helper code in `agent-creds/` (deny-Read) edited by copy-roundtrip. GUI/Screen-Sharing for consent/restart/LaunchAgent/`op`-admin ops (D20). Stop at ~63% context for a clean close.

## Carry-forward (deferred, not lost)
**Peter:** delete the retired 1Password SA (`WUE7…`) if not done. **Acceptance gate** ([P6-76]) + **v7 capstone/reconciliation** = the pre-Andreas milestones (after WS-4). **Travel agent = POST-6d** ([P6-85]). Per-turn tool/token ceiling ([P6-79] canary is the detective stand-in) + Telegram back-scroll ([P6-78]) → fold into runtime work. **Calendar follow-ups (optional, [P6-89]):** capture non-primary calendars' ICS → 1Password if write is ever wanted beyond `primary`; watch that act-directly solo-event writes behave well in real use. Standing: [F59]/[P6-66] rotations, S6 reconcile, SR-1/D23, 2026.4.22 pinned; model+framework currency ([P6-74]) owned by Andreas.
