# SESSION 6c.5 — Local documents (docx/xlsx edit → PDF → email-attachment) — OPENER
_Successor to 6c.4. The capable image already ships **LibreOffice 7.4.7.2 + python-docx + openpyxl** (6c.2, regression-green again in 6c.4), so the document *tools* exist; 6c.5 **wires the end-to-end workflow for both `pa` and `pro`** and adds the missing piece — **email-attachment support** on the draft path. Per `CAPABILITIES-ROADMAP-TO-6D.md` row 6c.5. **Signature-PNG insertion is DEFERRED (P6-33)** — build later against real examples; do NOT build it here. Protocol: Stage 0 verify-only → read-only recon → gated build (F17 if config touched; SHA-anchored) → end-to-end through a real agent turn (F26) → close._

## Why
Agents can already open/convert docs in-sandbox (LibreOffice + python-docx/openpyxl), but the **workflow isn't wired end-to-end** and the **gmail helper draft path has no attachment support**. 6c.5 makes the full loop real: open a `.docx`, edit it, export a PDF, and **draft an email with that file attached — surfaced for approval** (never auto-sent). Mirrors the established prepare-and-surface posture (gmail draft-only; calendar prepare-only; browser pre-submit).

## Stage 0 — VERIFY-ONLY (do NOT re-apply 6c.4)
Per the 6c.4 closure: gateway healthz 200 / pid **91510** (or current); **A-7 live==last-good `a7701f51d6d1`** (8926B) — note 6c.4 made **zero config changes**, so this should be unchanged; both containers `openclaw-sandbox:capable-2026-06 ✓`; models pa/pro `claude-opus-4-7` + thinking `high`. Browser live: `/workspace/browser_helper.py` sha **`0e2f72aa4c1b`** present in both containers; a one-shot `read https://example.com` green and the egress guard refuses `192.168.5.2:18789`. Office regression: `soffice --version` + `python3 -c "import docx,openpyxl"` green. Confirm the deviations log shows **P6-34 DONE / P6-35 + P6-36 OPEN**. **Confirm, don't redo.**

## Plan (read-only recon → gated)
1. **Recon (read-only).** Confirm the docx→edit→PDF path runs cleanly under the sandbox constraints (readOnlyRoot; LibreOffice first-run profile under **HOME=/tmp** — reuse the 6c.4 scratch lesson, force `HOME=/tmp` so the LO profile + conversion temp land in tmpfs, **not** the host workspace). Decide where finished docs/PDFs live (`/workspace` for the deliverable, `/tmp` for scratch). Inspect the **gmail_helper.py `draft` verb** — it currently has **no attachment support** (TOOLS.md: "draft never sends"); decide the minimal addition (a `--attach PATH` that base64-encodes a MIME part) vs an in-agent compose. **A-12:** verify the Gmail `drafts.create` MIME format empirically before scripting.
2. **HOME/scratch (carried, P6-31/P6-34).** Same resolution as the browser: force `HOME=/tmp` in any doc helper so LibreOffice/conversion scratch doesn't clutter `/workspace`. Keep only the intended deliverable in the workspace.
3. **Access path (gated).** Either extend `gmail_helper.py` with attachment support (it's RO-bound at `/opt` → **a change there needs the bind's source updated + recreate; a new bind needs a gateway restart = D20/GUI** — plan the venue) **or** add a small `docs_helper.py` delivered like the browser helper (into `/workspace`, no restart) if that keeps the cred-helper RO and avoids a GUI session. **Decision to weigh:** attachment support touches the *credential-bearing* gmail path (drafts into the real mailbox), so unlike the browser it is **not** a no-creds tool — prefer keeping it in the RO-bound `gmail_helper.py` (tamper-resistant) even at the cost of a GUI-session restart, OR justify the workspace route. Surface the trade-off.
4. **Apply + prove (F26).** Real turns: **Marie** opens a `.docx` (e.g. from Drive via google_helper or a workspace file), edits it, exports a PDF, and **drafts an email to herself/Peter with the PDF attached — surfaced, not sent**. **Charlotte:** confirm by role (her drafting is dormant in v1 — at minimum prove the docx→PDF half; gate the attachment-draft per the capability matrix §3b). Regressions: browser (read + egress refusal), google_helper, gmail list — green.

## Carried items to fold in
- **HOME=/tmp scratch** (P6-31/P6-34) — force it in any doc helper; keep `/workspace` clean.
- **Bootstrap truncation (P6-36)** — TOOLS.md is already ~52% truncated at injection; adding a docs section worsens it. Put the *invocation* in the (less-truncated) AGENTS.md line, keep the TOOLS.md section tight, and consider whether to raise `bootstrapTotalMaxChars` here or defer to 6c.7 hygiene.
- **Signature-PNG insertion — DO NOT BUILD** (deferred, P6-33). docx/xlsx + PDF + attachment only.
- **Docs/Sheets *writes* via API are blocked** (P6-32) — produce documents via LibreOffice/python-docx locally, not the Google Docs API.
- **P6-35 (egress hardening) — DECIDED: defer to 6c.7** (Peter, 6c.4). Do **not** action host-level egress hardening in 6c.5; it is a 6c.7 item. The browser egress-guard already in place stands. **P6-36 (bootstrap cap)** — default is to fold into 6c.7 hygiene; keep the TOOLS.md docs additions tight here so truncation doesn't worsen.

## Exit criteria
Both agents (by role, per matrix §3b) take a `.docx` → edit → PDF → **email draft with the PDF attached, surfaced for approval (never sent)**, through real agent turns; scratch stays out of `/workspace`; A-7 clean on any new sha; browser + Google + Gmail regressions green; TOOLS.md/AGENTS.md updated (both agents). **Signature insertion explicitly out of scope.**

## Watch-points
- **D20** — if the attachment path means a new/changed RO bind on `gmail_helper.py`, the gateway restart must run in a **Screen-Sharing GUI terminal** (6c.4 avoided this by using `/workspace`; the cred path may not have that luxury — decide early).
- **Credential-path caution** — attachment support touches the real-mailbox draft path; keep it draft-only (no send path exists; preserve that), prepare-and-surface, RO-bound if possible.
- **P6-24** sync defaults+pa+pro on any sandbox change. **P6-23** recreate after a host/bind fix, prove via a real turn. **F26** — live only through a real agent turn.

## Close
DEVIATIONS-LOG: record the docs workflow + attachment-draft path + HOME/scratch (new P6-code). Closure + fresh **6c.7 opener** (ops & reliability + chat-surface hygiene, per roadmap). Re-anchor any changed shas + the live `openclaw.json` sha. Commit `_session-docs/`. Resolve/advance P6-35 + P6-36 if Peter has decided.
