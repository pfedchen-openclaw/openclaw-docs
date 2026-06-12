# SESSION 6c.4 — Web (read + interactive headless browser) — OPENER
_Successor to 6c.3. The capable image already ships **Playwright + Chromium** (baked at `/opt/ms-playwright`, smoke-passed in 6c.2 and again in 6c.3), so this session makes **web read + interactive browser** real for Marie & Charlotte. Per `CAPABILITIES-ROADMAP-TO-6D.md` (row 6c.4). Protocol: Stage 0 verify-only → read-only recon → gated build (F17, SHA-anchored) → end-to-end through a real agent turn (F26) → close._

## Why
Agents can fetch simple pages, but cannot yet **read a JS-heavy link** or **operate a real portal** end-to-end. 6c.4 wires a browser access path (a `browser_helper.py` or in-agent Playwright pattern), mirroring the `google_helper.py` posture from 6c.3: **reads/navigation free; any state-changing submit is prepare-and-surface** (L1 — show Peter the intended action, never auto-submit credentials/payments/bookings).

## Stage 0 — VERIFY-ONLY (do NOT re-apply 6c.3)
Per the 6c.3 closure: gateway healthz 200 / **pid 91510** (or current) / **A-7 live==last-good `a7701f51d6d1`** (8926B); both containers `openclaw-sandbox:capable-2026-06 ✓`; models pa/pro `claude-opus-4-7` + `thinkingDefault:"high"`; binds = `/workspace` + `/opt/gmail_helper.py` + `/opt/google_helper.py` + `/creds/google.json`; a one-shot in-container `playwright --version` + `python3 -c "import playwright"` green; `google_helper.py calendar-list` + Gmail helper still list for both mailboxes. **Confirm, don't redo.** Verify the deviations log shows **P6-32 DONE / P6-1 discharged**.

## Plan (read-only recon → gated, F17)
1. **Recon (read-only):** confirm the in-container Chromium launch contract — **`--no-sandbox --disable-dev-shm-usage`** is mandatory (P6-31; no userns, small /dev/shm). Decide read-vs-interactive surface: navigation + DOM/text extraction + screenshot/PDF (free) vs. form-fill/click/submit (**prepare-and-surface**). Check `sandbox.docker` browser knobs (the schema has a `--browser` recreate flag and possibly a browser-container split — A-12 verify).
2. **HOME/scratch (carried from P6-31):** agent-exec runs with **`HOME=/workspace`** (overrides the image-baked `HOME=/tmp`), so Chromium user-data-dir + cache land in the host-mounted workspace and accumulate. Decide: set `sandbox.docker.env.HOME=/tmp` (verify it overrides the runtime default; P6-24 sync defaults+pa+pro) **or** point Playwright at an explicit `/tmp` user-data-dir. Keep the workspace clean.
3. **Access path (gated):** author the browser helper / pattern (Playwright, `--no-sandbox --disable-dev-shm-usage`), bound RO like the other helpers (P6-24 binds sync; gateway restart + P6-23 recreate). **Interactive submits are L1 prepare-and-surface** — same posture as calendar/payments/external-sends.
4. **Apply + prove (F26):** a real Marie turn (a) **reads a JS-heavy link** (extract the content) and (b) **operates a real portal end-to-end** — the roadmap target is the **Mews check-in**; pick a safe, reversible portal action and surface the final submit for approval rather than auto-committing. Regression: Google helper (calendar/drive) + Gmail + docx/xlsx still green.

## Carried items to fold in
- **Chromium flags** `--no-sandbox --disable-dev-shm-usage` (P6-31) — bake into the helper.
- **HOME=/workspace clutter** (P6-31) — resolve scratch placement here (browser writes a lot).
- **Docs/Sheets writes** remain blocked on an owned GCP project (P6-32) — not a 6c.4 concern, but if a portal flow needs doc output, route via LibreOffice/Drive-export, not the Docs API.

## Exit criteria
Marie reads a JS link **and** drives a real portal to the point of a surfaced-for-approval submit, through real agent turns; browser scratch is clean (HOME/scratch decision applied, P6-24 synced); A-7 clean on any new sha; Google + Gmail + office regressions green; TOOLS.md updated with the browser verbs (both agents).

## Watch-points
- **P6-31** Chromium needs `--no-sandbox --disable-dev-shm-usage`; HOME=/workspace scratch. **P6-4 / SR-2 posture** — no autonomous submits; L1 surface-for-approval (credentials/bookings/payments never auto-entered). **P6-24** sync defaults+pa+pro on any sandbox change. **P6-23** recreate after host/config fix, confirm via a real turn. **D20** if a gateway restart is needed (binds/env), run in a Screen-Sharing GUI terminal.

## Close
DEVIATIONS-LOG: record the browser access path + read-vs-interactive posture + HOME/scratch resolution (new P6-code). Closure + fresh **6c.5 opener** (docs + signature, per roadmap). Re-anchor openclaw.json + helper shas. Commit `_session-docs/`.
