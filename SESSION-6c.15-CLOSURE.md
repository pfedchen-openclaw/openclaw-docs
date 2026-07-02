# SESSION 6c.15 — WS-4: Marie's send identity `marie@fedchenkov.com` LIVE (F26 PASS) — CLOSURE
_2026-07-02. **GUI/Screen-Sharing session** (`launchctl managername` = `Aqua`; D20 satisfied — consent, sandbox recreate, gateway restart all available). The deferred 6c.12/6c.14 headline shipped: Marie now sends as her own durable, deliverable identity, gated to prepare-and-surface and locked at the tool floor to that identity only. New code **[P6-90]**. `openclaw.json` changed once (add one pa-only cred bind): `a8783dcd17dc`/9993B → **`8a6e2a19d9d5`/10096B** (A-7 clean, live==last-good). One billed turn total — the F26 send (Sonnet, real Telegram path)._

## Stage 0 — GREEN core, one AMBER
- **Core GREEN:** A-7 clean at open (`a8783dcd17dc`/9993B, 600/staff); pin **2026.4.22**; both containers `capable-2026-06`; `google_helper.py 46a9243f954a` bound both containers with calendar edit-verbs ([P6-89] live); OAuth alive (marie/pfedchen reads exit 0); 1Password SA `openclaw-agents` (`6LYBZQU6…`) green; deviations re-anchored `7779f76026e2`.
- **AMBER (carried):** the deferred overnight cost-hardening check did **not** run overnight — scripts/plists are correct and LIVE, but the LaunchAgents were only bootstrapped ~midday 07-02 (6c.14 SSH couldn't, D20), so no 23:00/07:00 suppression or 06:55 reset fired overnight; beats ran on cheap Sonnet + a manual freshstart/restart at 13:26 applied the 120K cap live. **Loaded+live now → tonight is the first real overnight test.** Detail in [P6-90] Stage-0.

## What shipped — [P6-90] (full detail in the deviations log)
- **Decisions (Peter):** **second mailbox** (Marie keeps pfedchen Gmail-read + calendar; adds marie@ read+send — not a full swap); **reuse the durable `openclaw-pf` OAuth client** (Internal-in-org app deferred to Andreas); agents use **function-as-surname**, From **displays first name only**.
- **Phases 1–4 (Peter's browser, guided):** Workspace Business Starter trial (£5.90/user/mo, 14-day), admin `peter@fedchenkov.com`; `fedchenkov.com` verified; MX `smtp.google.com` via the Google↔Cloudflare auto-integration; standard user `marie@fedchenkov.com`; bidirectional mail confirmed. **Deliverability:** SPF + DMARC added at Cloudflare, DKIM auto-published + authenticating.
- **Phase 5 (my wiring):** Peter allowlisted the client as **Trusted** in Admin; slim **gmail.modify-only** consent → durable `agent-creds/marie-fedchenkov.json` `43353284e50f`/416B. Added **`send`/`send-draft`** to `gmail_helper.py` (`d9858a84b60d`→`5bc5d7b73a4e`/10646B, bind-source + mirror) with a **`SEND_IDENTITIES={marie@fedchenkov.com}` floor guard** — Peter's pfedchen + Charlotte's Soveren stay draft-only at the tool floor, un-routable by any prompt. Bound `marie-fedchenkov.json → /creds/marie-send.json` (pa only) via F17 staged `openclaw.json` edit + `sandbox recreate --agent pa` + gateway restart. Docs reconciled (TOOLS.md `8fa873cd295c`, AGENTS.md `a6aba8f23e51`).
- **F26 PASS (real Telegram→gateway→sandbox, Sonnet):** Marie drafted → surfaced the exact text → waited for approval → sent on "go ahead". Independently verified: pa container mounts the send cred; message in marie@ **Sent**; landed in `pfedchen` **INBOX** (not Spam), From "Marie". Prepare-and-surface held; choke-point enforced.

## End-state (anchors)
- **`openclaw.json`: `8a6e2a19d9d5`/10096B** — A-7 clean, live==last-good, 600/staff. Pin **2026.4.22**; both containers `capable-2026-06`; gateway restarted (config + 120K cap + 2h beat live).
- **New host files:** `agent-creds/marie-fedchenkov.json` `43353284e50f`/416B/600 (deny-Read).
- **Changed files (SHA-anchored):** `agent-creds/gmail_helper.py` + `agent-tools/gmail_helper.py` `5bc5d7b73a4e`/10646B (644); `workspace-pa/TOOLS.md` `8fa873cd295c`/19379B; `workspace-pa/AGENTS.md` `a6aba8f23e51`/12038B. **Deviations:** [P6-90] appended additive; log now `960b55daff29`. _(workspace-pa + agent-creds are live-runtime, untracked by the top-level repo; only `_session-docs` is committed.)_

## Carried → 6c.16 (deliberately, not lost)
- **Overnight automation first real test:** confirm at next open that 23:00 suppression + 06:55 reset fired from the logs (`heartbeat-nightwindow.log`, `session-freshstart.log`, `heartbeat.night`/`heartbeat.resumed-at`).
- **Andreas backlog (Peter req):** a NEW OAuth app owned by the **fedchenkov.com org** (Internal consent) to replace reuse of pfedchen-owned `openclaw-pf` — minor while durable+trusted → in PRE-COS-BACKLOG.
- **DMARC** `p=none` → tighten to quarantine/reject once reputation builds.
- **Peter:** delete retired 1Password SA (`WUE7…`) if not done ([P6-88]).
- **Stale doc:** `AGENTS.md:16` "Drive is READ-ONLY … no upload yet" contradicts live drive.file write ([P6-83]) — reconcile next docs pass.
- **Pre-Andreas milestones:** acceptance gate ([P6-76]) + v7 capstone/reconciliation. Travel = post-6d. Standing: [F59]/[P6-66] rotations, 2026.4.22 pinned, model/framework currency ([P6-74]) → Andreas.

## Honest note
The core WS-4 build was straightforward; the judgement was in two places. First, the **security floor**: rather than just "add a send verb," I gated send to a single dedicated identity in the helper itself, so Peter's real Gmail and Charlotte's mailbox stay send-incapable at the tool layer regardless of what any prompt tells the agent — a legible choke-point, not an enumerated rule. Second, **cost/faithfulness for F26**: the memory says `openclaw agent` mis-tests, so I used the real Telegram path, but that risked Opus; Marie's base model is Sonnet so the faithful path was also the cheap path — one billed turn, verified end-to-end by reading the actual mailboxes, not her self-report. The one amber (overnight automation didn't run overnight) turned out not to be a regression but a not-yet-bootstrapped-live artefact of 6c.14 being SSH; it's live now and will prove itself tonight.

---
_Attachments for 6c.16: this closure + `SESSION-6c.16-OPENER.md` + `PHASE-6-DEVIATIONS-LOG.md` (through P6-90) + `CAPABILITIES-ROADMAP-TO-6D.md` + `JUDGEMENT-DOCTRINE.md`._
