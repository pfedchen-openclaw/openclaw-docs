# SESSION 6c.2 — Capable Sandbox Image — OPENER
_Successor to 6c.1. **Supersedes `SESSION-6c2-CALENDAR-HELPER-OPENER.md`** — the image jumps ahead of calendar because it makes calendar/web/docs all native-in-container instead of bespoke helpers. Foundational and isolated: this session does **only** the image swap, nothing rides with it. Per `CAPABILITIES-ROADMAP-TO-6D.md`. Protocol: Stage 0 verify-only, diagnose read-only, greenlight before any state-changing step, F17 hygiene, SHA anchors._

## Why
The clean `openclaw-sandbox:bookworm-slim` image has only stdlib Python — every capability so far has meant hand-rolling a zero-dependency helper, which is the fragile scaffolding we've decided to stop building (ADR §2 in the roadmap). Replacing it with a capable, maintained image makes Office/PDF/browser/Google all native inside the container, shared by every agent, while keeping the Strategy-2 isolation. Swapping is a supported config change (`sandbox.docker.image`), and the image is portable to plain Docker (longevity).

## Stage 0 — VERIFY-ONLY (do NOT re-apply 6c.1)
Per the 6c.1 closure ledger: gateway `healthz` 200 / pid present / A-7 (live==last-good `bfb3375d17f9b9b7` unless changed since); Gmail helper is a **file** (sha12 `5fd20fd379a7`, 5689B); a one-shot `--read-only --network bridge` + binds + helper `list --max 1` returns real mail for both mailboxes; Marie AGENTS `2966a8599e13`/TOOLS `d4001c96749d`, Charlotte AGENTS `bd0fa295eee0`/TOOLS `69876f4d110d`; P6-22 present in both. **Confirm, don't redo.**

## Build target (capable image)
A Dockerfile (FROM debian:bookworm or the OpenClaw base) installing:
- `google-api-python-client` + `google-auth-oauthlib` (Gmail/Calendar/Drive/Docs — standard client)
- `python-docx`, `openpyxl`
- **LibreOffice headless** (writer+calc) + fonts/locale (.docx/.xlsx ⇄ PDF)
- **Playwright + headless Chromium** (`playwright install --with-deps chromium`)
- retain curl, jq, openssl, python3/pip
Tag e.g. `openclaw-sandbox:capable-2026-06`. Built on the Mini via Colima (expect ~1.5–2 GB, ~20–40 min build).

## The hard watch-point — readOnlyRoot vs LibreOffice/Chromium
The stdlib helper never wrote to disk, so `readOnlyRoot:true` was fine. **LibreOffice and Chromium write** (profile dirs, `/tmp`, user-data-dir) and will fail read-only. This session must provide **writable scratch** while keeping the rootfs read-only:
- A writable `/tmp` (tmpfs) and a writable HOME/scratch (e.g. `HOME=/workspace/.scratch`, which is already rw, or a tmpfs mount).
- **Verify first (A-12):** how OpenClaw's `sandbox.docker` block exposes `tmpfs` / extra writable mounts / env (`HOME`). Do not assume — confirm against the pinned 2026.4.22 schema before configuring.
Resolve this in the smoke before declaring the image good — a docx→pdf convert and a Playwright launch are the two tests that exercise the write paths.

## Plan (greenlit; read-only recon → gated state-changes, F17)
1. **Recon (read-only):** confirm `sandbox.docker` schema keys for image/tmpfs/mounts/env on this build; confirm Colima disk headroom; current per-agent sandbox blocks.
2. **Author + build** the Dockerfile on the Mini; verify the image exists and the toolchains are present (`soffice --version`, `python3 -c "import docx, openpyxl, googleapiclient"`, `playwright --version`).
3. **Stage config (F17):** point `sandbox.docker.image` at the new tag in `agents.defaults.sandbox` **and** the per-agent `pa`/`pro` blocks (P6-24 drift — keep them in sync); add the writable-scratch (tmpfs/HOME) settings. Diff to show only image + scratch changed. Atomic mv + chmod600/chgrp staff.
4. **Apply:** validate; restart gateway only if the image/scratch keys are restart-triggers (verify — else recreate suffices); **recreate pa+pro `--force` AFTER the image is built and config committed** (P6-23 ordering — never before).
5. **Smoke — through a real agent turn (new def-of-done):** in the recreated agent container, exercise each toolchain end-to-end:
   - Google: a live Calendar/Gmail API call via `google-api-python-client` (proves libs + egress + the existing durable creds).
   - Browser: Playwright launches Chromium and loads a page.
   - Docx: `python-docx` creates a doc; LibreOffice converts it to PDF (exercises writable scratch).
   - Xlsx: `openpyxl` reads/writes a sheet.
   Then a **real Marie turn** that uses at least one new toolchain, to satisfy the def-of-done.

## Exit criteria
Both agents recreated on the capable image; all four toolchains smoke-pass **inside a real agent container**; writable-scratch confirmed working under readOnlyRoot; A-7 clean on the new config sha; a real agent turn has exercised a new capability. Gmail (existing helper) still works (regression check).

## Watch-points
- **P6-23:** verify the image is built and present before recreate; recreate only after; confirm via a real turn, not just `inspect`.
- **P6-24 drift:** update image (and any scratch mounts) in **both** the defaults and the per-agent `pa`/`pro` blocks; they must stay in sync.
- Image build time/size; Playwright system deps on arm64 (bookworm); LibreOffice first-run profile under the writable HOME; fonts for PDF fidelity.
- Keep `mode:all` and `network:bridge`; egress unchanged. Secrets stay outside the container.

## Close
DEVIATIONS-LOG: record the image swap (capable image replaces slim; the readOnlyRoot/writable-scratch resolution; image-as-portable-longevity asset) and re-anchor `openclaw.json` + per-agent block shas. Short closure. Fresh **6c.3 opener** (Google APIs: Calendar + Drive + Docs/Sheets, durable OAuth, SR-2 before calendar write, P6-4).
