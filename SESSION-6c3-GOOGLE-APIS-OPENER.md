# SESSION 6c.3 — Google APIs (Calendar + Drive + Docs/Sheets) — OPENER
_Successor to 6c.2. The capable image now ships `google-api-python-client` + `google-auth-oauthlib` natively, so this session makes **Calendar/Drive/Docs/Sheets** real for Marie & Charlotte on durable OAuth. Per `CAPABILITIES-ROADMAP-TO-6D.md`. Protocol: Stage 0 verify-only → read-only recon → gated build (F17, SHA-anchored) → end-to-end through a real agent turn (F26) → close._

## Why
6c.2 proved the Google libraries are present in-container and that egress + durable creds work (Gmail helper regression), but did **not** wire `google-api-python-client` to a live API. Marie's morning digest, calendar awareness, and any doc/sheet handling all need real Google API access. This is the direct successor to the deferred **P6-1 calendar** item.

## Stage 0 — VERIFY-ONLY (do NOT re-apply 6c.2)
Per the 6c.2 closure: gateway healthz 200 / pid 56991 / **A-7 live==last-good `9477ab40a92a`** (unless changed since); both containers on **`openclaw-sandbox:capable-2026-06 ✓`** (no mismatch); models pa/pro `claude-opus-4-7` + `thinkingDefault:"high"`; image in defaults+pa+pro; binds `/workspace`+`/opt/gmail_helper.py`+`/creds/google.json` intact; a one-shot in-container `python3 -c "import googleapiclient"` + `soffice --version` still green; Gmail helper still lists for both mailboxes. **Confirm, don't redo.** (If drifted, characterise before touching.)

## Central decision (resolve first, read-only) — durable Google OAuth
The bound `/creds/google.json` holds the **gmail.modify** refresh token on **gog's published client** (durable, no TTL cliff — 6c.1). Calendar/Drive/Docs need **additional scopes**. Decide the scope/credential strategy **before** building (A-12, structure-only — never print token values; the creds files are deny-Read / gated):
- **Option A (preferred): re-consent on gog's published client** with the superset of scopes (gmail.modify + calendar + drive.file + documents + spreadsheets), producing one durable refresh token per mailbox — replaces/extends each `*-gmail.json`. Mirrors the 6c.1 host-side one-time consent pattern (`gmail_consent.py`).
- **Option B (reject unless A blocked):** the 7-day Testing-mode calendar-backup token (**F44**) — TTL cliff, operationally fragile.
- Per-mailbox isolation stays (pa→pfedchen, pro→soveren; single-file binds). Secrets remain **outside** the container (host-side consent; bind the refreshed creds RO).

## Plan (read-only recon → gated, F17)
1. **Recon (read-only):** confirm google-api-python-client can consume the existing creds JSON shape (client_id/secret/refresh_token/token_uri present?) — if the gmail helper's creds lack fields the library needs, that dictates the re-consent. Confirm the gog published-client scope-add path. Verify Calendar API is the direct Google API (gog calendar is broken — **F46**).
2. **Host-side consent (gated):** extend each mailbox's durable token to the new scopes (Option A). Verify display-immune (sha + byte-length only; **never print token values**). Bind refreshed creds RO into both containers (re-use `/creds/google.json` target or add a second creds file — decide).
3. **Helper/tooling (P6-24):** author the Google access path the agents call (a thin `google_helper.py` using google-api-python-client, OR direct in-agent python) — list/read first. **Calendar/Doc/Drive *writes* are L1 prepare-and-surface (SR-2 / P6-4):** drafts/proposed events surfaced for approval, **no autonomous create/modify/send** (same posture as payments P6-3, external sends P6-25). Document each verb in **TOOLS.md** (both agents) — this also discharges the carried 6c.2 "TOOLS.md advertises the new toolchains" item (Office/PDF/browser-flags + Google).
4. **Apply + prove (F26):** a real Marie turn reads her **calendar** (today's events) and her inbox via the library; a real Charlotte turn reads the Soveren calendar; one **prepared-but-not-sent** calendar event surfaced for approval (SR-2). Regression: Gmail helper + docx/xlsx/Chromium still green.

## Carried items to fold in (from 6c.2 / P6-31)
- **Agent-exec `HOME=/workspace`** overrides the baked `HOME=/tmp` → office/browser scratch lands in the host workspace. If wiring browser/office helpers here, decide whether to set `sandbox.docker.env.HOME=/tmp` (verify it overrides the runtime default; P6-24 sync defaults+pa+pro) or point tools at explicit `/tmp` scratch dirs. Keep the workspace clean.
- **Chromium flags** `--no-sandbox --disable-dev-shm-usage` — bake into any browser helper / TOOLS.md.
- **TOOLS.md capability documentation** for LibreOffice (docx/xlsx⇄PDF), python-docx/openpyxl, Playwright — currently the agents *can* use these but their docs don't advertise them. Add here so they reach for them.

## Exit criteria
Durable Google OAuth live for both mailboxes (Option A unless blocked); Marie & Charlotte read Calendar (+ Drive/Docs as scoped) via google-api-python-client through real agent turns; calendar/doc **writes are prepare-and-surface only** (SR-2 proven on one event); TOOLS.md updated (Google + the 6c.2 toolchains, P6-24); A-7 clean on the new sha; Gmail + office/browser regressions green.

## Watch-points
- **F46** gog calendar broken → direct Google Calendar API only. **F44** avoid the 7-day token. **F21/F51** never print token values; operate creds structure-only; host-side consent, secrets outside the container. **P6-4 / SR-2** no autonomous calendar/doc writes — L1 surface-for-approval. **P6-24** sync defaults+pa+pro on any sandbox change. **P6-23** if any recreate, after host/config fix, confirm via a real turn.

## Close
DEVIATIONS-LOG: record the OAuth scope strategy + the Google access path (new P6-code), discharge **P6-1**; re-anchor openclaw.json + creds shas. Closure + fresh **6c.4 opener** (per roadmap). Commit `_session-docs/`.
