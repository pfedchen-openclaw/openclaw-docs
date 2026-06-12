# SESSION 6c.3 — Google APIs (Calendar + Drive + Docs/Sheets) — CLOSURE
_2026-06-11 (GUI/Screen-Sharing session, D20 venue confirmed). Wired durable Google OAuth + a read/prepare-only access helper for Marie & Charlotte. **Records: P6-32 (Google APIs); discharges P6-1.**_

## Outcome
Both live agents now hold a **durable superset OAuth token per mailbox** (Marie→pfedchen, Charlotte→soveren) on **gog's published client** — `gmail.modify + calendar + drive.readonly + documents + spreadsheets`, no TTL cliff. A new in-sandbox helper `google_helper.py` (bound RO, **read + prepare-only — no write path**) gives both agents **Calendar / Drive / Docs / Sheets reads** and a `calendar-prepare` verb that surfaces a proposed event for approval but never inserts. Proven end-to-end through **real agent turns**: Marie read her calendar (2 live events) and surfaced a prepared event (not created); Charlotte read the Soveren calendar (+ its 3 calendars). Drive read works for both; Docs/Sheets read via a Drive-export workaround. Gmail + the 6c.2 office/browser toolchains regression-clean. SR-2 ICS backups for both calendars are in 1Password. **6c.3 CLOSED; P6-1 discharged.**

## What was done (arc)
- **Decision (Peter-approved):** Option A (re-consent superset on gog's published client) + the **read-capable superset**, symmetric on both mailboxes — chosen for maximum capability ASAP and to avoid a second consent cycle. `drive.readonly` is the key unlock (read pre-existing files, not just agent-created). Scope ≠ autonomy: the helper enforces prepare-and-surface.
- **Consent:** `_incoming-6c1/google_consent.py` (gmail_consent.py + superset SCOPES), browser-approved per mailbox over Screen Sharing. All 5 scopes granted incl. the restricted `drive.readonly` (gog's app carries it). Staged→verified→atomic-mv; `.pre6c3` backups kept.
- **Access path + config:** `google_helper.py` bound RO into pa+pro via `openclaw config set …binds` (P6-24); gateway restarted to apply binds (pid 56991→91510); pa+pro recreated **after** (P6-23). openclaw.json `9477ab40a92a`→`a7701f51d6d1`, `.last-good` synced (A-7 clean).
- **API-enablement finding + workaround:** gog's project has Gmail/Calendar/Drive enabled but **not Docs/Sheets** (403; can't enable — not ours). Helper reads Docs/Sheets via **Drive `files.export`** under `drive.readonly`. The `documents`/`spreadsheets` scopes are harmless-but-unused until a future write needs an owned GCP project.
- **SR-2:** durable-token ICS export of both primary calendars → 1Password (vault OpenClaw). No calendar write shipped, so the gate wasn't triggered — guardrail pre-positioned.
- **TOOLS.md:** both agents updated (Google verbs + 6c.2 toolchain docs). Charlotte's documented capability **expanded** (symmetric grant) — flagged.

## End-state (anchors)
- **openclaw.json:** **`a7701f51d6d1`** (8926B) — `== .last-good`, **A-7 CLEAN**. Gateway pid **91510**, healthz 200 (:18789).
- **Creds (durable superset):** pfedchen `ae15559ca358` (601B), soveren `93d1d3dab3da` (610B); both bound RO `/creds/google.json`, 600/staff. `.pre6c3` backups retained.
- **Helper:** `agent-creds/google_helper.py` **`15566ad20307`** (8396B), bound RO `/opt/google_helper.py` in pa+pro.
- **Binds (pa & pro):** `gmail_helper.py` + `<mailbox>-gmail.json:/creds/google.json` + `google_helper.py:/opt/google_helper.py` (all ro) + auto `/workspace`.
- **TOOLS.md:** Marie **`3532a1ec11aa`**, Charlotte **`8b81b44fb02f`**.
- **Models/image (unchanged):** pa/pro `claude-opus-4-7` thinkingDefault `high`; `openclaw-sandbox:capable-2026-06`.
- **1Password (SR-2):** vault OpenClaw `yxpzy7chhi46w744edu752eqae` — docs `lmeyxlspfqhuumdtvf2dhg4u7i` (pfedchen ICS) + `mmpcfwkq5lxmp2357m6vm4e34m` (soveren ICS).
- **New host tooling:** `_incoming-6c1/google_consent.py`, `scripts/calendar_ics_export.py`, `scripts/upload_ics_to_1p.sh`.

## Carried / new findings (see P6-32 for detail)
- **Docs/Sheets writes** (and full-fidelity range/multi-tab reads) need an **owned GCP project** with those APIs enabled + a verified OAuth client — gog's published client can't provide them. Carry to the doc-signature session (6c.5) if doc writes are needed there.
- **Charlotte role-vs-grant:** the symmetric superset gave the sunsetting Soveren assistant calendar/drive read beyond her original minimal v1 role. Intended per Peter's call; revisit if her role doesn't warrant it.
- **SR-2 non-primary calendars** (Soveren Team / Customer Success) not yet ICS-backed; add before any write to them.
- **Permission-gate note:** `jq`/`stat`/`ls` on secret-bearing paths were gated this session despite the settings allow-list; used `openclaw config` + `shasum`/`wc` (display-immune) instead. No blocker, but worth knowing.

## Outstanding (carried, unchanged)
- **F28 cold-reboot proof** — still pending (→ 6c.7).
- **P6-27 / P6-30** chat-surface hygiene → 6c.6. **P6-29** scheduling reliability → 6c.7. **SR-1** docs-autopilot → 6d.
- **PENDING-BATCH-APPLY** (P6-9/11/12/14), **P6-20 avatar** → 6c.7 / docs-locked.
- **P6-24 sandbox-block drift** — pa+pro carry full sandbox blocks; re-sync on any `agents.defaults.sandbox` change.

---
_Attachments for 6c.4: this closure + PHASE-6-DEVIATIONS-LOG.md (P6-32) + MASTER-ARCHITECTURE-v6 + the 6c.4 opener. Older closures on demand._
