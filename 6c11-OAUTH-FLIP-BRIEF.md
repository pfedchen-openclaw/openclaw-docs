# 6c.11 — F44 OAuth flip: handoff brief (Peter's GCP action + my wiring)
_Generated 6c.11 (2026-06-27). Peter chose "do the Internal-OAuth flip now." This brief is the recon result + the precise steps. **There is one decision in it** (the publishing-status fork below) that I need your call on before the consent step, because "Internal" cannot serve the account Marie reads today. Heartbeats are PAUSED (cap in place until ~1 Jul), so this is not time-critical — it can be done whenever you have 20 min at the Mini, ideally folded into 6c.12._

## The problem (F44, restated)
Refresh tokens for an OAuth app in **"Testing"** publishing status expire after **7 days** → both agents' Google (Gmail/Calendar/Drive) goes dark weekly (`invalid_grant`). The 6c.3 setup borrowed **gog's** published client to dodge this, but it died anyway (we don't control gog's app state). **The durable fix is an OAuth client on a GCP project _you own_, in a publishing status that doesn't expire tokens.**

## The fork I need your call on — "Internal" vs "In production"
"Internal" publishing status gives durable tokens with **no verification and no warning screen** — but it **only authorises users inside the GCP project's own Google Workspace organisation**. The accounts in play:

| Agent | Account today | Can use "Internal"? |
|---|---|---|
| Marie (`pa`) | **pfedchen@gmail.com** (consumer Gmail) | **No** — a consumer Gmail is not in any Workspace org. |
| Charlotte (`pro`) | peter.fedchenkov@soveren.io (Soveren Workspace) | Only if the project sits in the **Soveren** org (you'd need to be its Workspace admin; Soveren is being sunset). |
| Marie's future identity | **marie@fedchenkov.com** (6c.12 / WS-4) | **Yes** — if you own a fedchenkov.com Google Workspace. This is the clean long-term home. |

So **"Internal" is the right durable answer for fedchenkov.com Workspace accounts, but it cannot fix Marie's current pfedchen@gmail.com inbox.** Two coherent paths:

- **Path A — durable now, on the current accounts (recommended for today):** owned GCP project, OAuth consent screen **External → published "In production"**. Production status removes the 7-day expiry for *any* account incl. consumer Gmail. Because gmail.modify/drive are restricted scopes and the app is unverified, you click through a one-time "Google hasn't verified this app → Advanced → Go to (unsafe)" screen at consent — harmless for single-user personal use; tokens are then durable. Works for **both** current mailboxes immediately.
- **Path B — clean long-term, needs the email migration first:** stand up fedchenkov.com Workspace + marie@fedchenkov.com (6c.12 WS-4), GCP project in that org, consent screen **Internal**. Durable, no warning — but only once the agents read fedchenkov.com mailboxes, which is a bigger migration.

**My recommendation:** **Path A now** (kills the weekly death on the real accounts this cycle, and enabling Docs/Sheets APIs on the owned project also unblocks 6c.12 Drive/Sheets-write), then **graduate to Path B / Internal** when marie@fedchenkov.com lands in 6c.12. Confirm which and I'll wire it.

## Peter's steps (GCP console, ~20 min) — Path A
1. **Create a project** you own: console.cloud.google.com → new project (e.g. "openclaw-agents").
2. **Enable APIs** (APIs & Services → Library): Gmail API, Google Calendar API, Google Drive API — **and** Google Docs API + Google Sheets API (the owned project lets us enable these; gog's didn't — [P6-32] — so this also unblocks 6c.12 doc/sheet *write*).
3. **OAuth consent screen:** User type **External**; fill the minimal app info; add the restricted scopes (gmail.modify, calendar, drive — drive.file for write later); **then "Publish app" → status "In production"** (this is the bit that kills the 7-day expiry). No Google verification needed for our single-user case — the unverified warning at consent is expected and safe to click through.
4. **Create credentials:** OAuth client ID, application type **Desktop app**. Download the client-secret JSON.
5. Hand me the client-secret JSON path on the Mini (drop it in `~/.openclaw/credentials/` — it's deny-Read for me, I'll reference it by path only, never read its values).

## My wiring steps (after you've done the above) — restart-free
1. Stage the downloaded client as `credentials/owned-client-secret.json` (0600).
2. Re-run the existing consent flow per mailbox, pointing at the owned client (this is the **one GUI/Screen-Sharing step** — it opens a browser for your Google login + consent; D20):
   ```
   python3 _incoming-6c1/google_consent.py --client ~/.openclaw/credentials/owned-client-secret.json \
       --account pfedchen@gmail.com --out ~/.openclaw/agent-creds/pfedchen-gmail.json
   python3 _incoming-6c1/google_consent.py --client ~/.openclaw/credentials/owned-client-secret.json \
       --account peter.fedchenkov@soveren.io --out ~/.openclaw/agent-creds/soveren-gmail.json
   ```
3. The new durable refresh tokens overwrite the existing cred JSONs in place — **no bind change, no gateway restart** (the helper reads the file content at runtime; this is how 6c.3 worked once the bind existed). Display-immune verify: scopes array + refresh_token-present + sha-12/bytes, never the token value (F21/F51).
4. **Verify durability:** a `google_helper.py` calendar/Gmail read succeeds immediately; the real proof is **no `invalid_grant` after 7+ days** — the watchdog now escalates that automatically ([P6-78] block B) if it recurs.

## Notes
- The consent step needs a browser on the Mini → **Screen-Sharing GUI terminal (D20)**; everything else is SSH-safe.
- Once on the owned project with Docs/Sheets enabled + a drive.file scope, **6c.12 Drive/Sheets-write** is unblocked (add the `drive-upload`/sheet-write verbs to `google_helper.py` then — [P6-65]).
- After restore, reconcile Marie's MEMORY.md line "I don't have reliable calendar access" (stale since 6c.3; left in place this session because it's accidentally protective while OAuth is dead).
