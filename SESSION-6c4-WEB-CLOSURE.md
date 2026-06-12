# SESSION 6c.4 — Web (read + interactive headless browser) — CLOSURE
_2026-06-12 (SSH session — no gateway restart needed, so D20 did not bind). Wired a JS-capable in-sandbox headless browser for Marie & Charlotte. **Records: P6-34 (browser path), P6-35 (egress security finding), P6-36 (bootstrap-truncation + clutter). Discharges the P6-31 HOME/scratch carry.**_

## Outcome
Both live agents can now **read JS-heavy pages** and **drive web portals to a surfaced pre-submit** through their actual sandbox runtime. The access path is `browser_helper.py` (Playwright over the image-baked Chromium), delivered into each agent's `/workspace` — **reads/navigation free; any state-changing submit is L1 prepare-and-surface** (screenshot the pre-submit, surface for approval; `commit` is a separate, explicitly-flagged, logged post-approval verb). **Credentials & payment fields are never auto-entered** (the helper refuses them), and an **egress guard** refuses any navigation/sub-request to loopback/private/metadata/host targets — the browser cannot reach the gateway, host SSH, or internal services. Proven end-to-end via **real agent turns** (F26): Marie read a JS-only page and drove a login portal to a non-committed pre-submit screenshot; Charlotte read a JS page, had the egress guard **refuse the host gateway**, and passed a calendar regression. Gmail (both mailboxes), google_helper calendar, and the office toolchain are regression-clean. **Zero `openclaw.json` change — A-7 stayed `a7701f51d6d1` throughout.** **6c.4 CLOSED.**

## What was done (arc)
- **Stage 0 (verify-only).** Deviations log current (P6-32 DONE / P6-1 discharged, latest P6-33). Confirmed gateway pid **91510** / healthz 200; **A-7 live==last-good `a7701f51d6d1`** (8926B); both containers `capable-2026-06 ✓`; models pa/pro `claude-opus-4-7` + thinking `high`; binds intact; in-container `playwright --version` (1.60.0) + `import playwright` green; google_helper + gmail helper list for both mailboxes.
- **Recon (read-only).** Reproduced the Chromium launch contract (`--no-sandbox --disable-dev-shm-usage`; `/dev/shm` = 64MB; readOnlyRoot=true with tmpfs `/tmp`,`/run`,`/var/tmp`; `/workspace` host-mounted virtiofs). **Egress probes surfaced P6-35** (see below). Found `/workspace` host source = `workspace-{pa,pro}/` (the agent doc files already live there) → the delivery path that avoids a D20 gateway restart.
- **Build (gated, reversible).** Authored `browser_helper.py` (canonical `agent-tools/browser_helper.py`, readable — not in deny-listed `agent-creds/`). Smoke-tested in a faithful throwaway container (read, JS render, screenshot, egress refusals, credential/commit refusals, attribute-selector parsing) — fixed one real bug (split `SEL=VAL` on first `=` at bracket-depth 0) — then deployed identical copies to `workspace-pa/` + `workspace-pro/`. **No bind, no gateway restart, no recreate, no config change.**
- **Docs (P6-24).** Browser section added to both TOOLS.md; a prepare-and-surface browser-autonomy line (with a **self-sufficient invocation path**, P6-36) added to both AGENTS.md.
- **F26 (real agent turns, `claude-opus-4-7`).** Marie + Charlotte exercised the capability end-to-end (details below). Regressions green.

## End-state (anchors)
- **openclaw.json:** **`a7701f51d6d1`** (8926B) — `== .last-good`, **A-7 CLEAN, untouched this session**. Gateway pid **91510**, healthz 200.
- **Helper:** `agent-tools/browser_helper.py` (canonical) + `workspace-pa/browser_helper.py` + `workspace-pro/browser_helper.py` — all sha **`0e2f72aa4c1b`** (13936B), at `/workspace/browser_helper.py` in both live containers.
- **Marie (`pa`):** TOOLS **`4505f3bfacca`** (8300B), AGENTS **`f71340cfeb53`** (7337B).
- **Charlotte (`pro`):** TOOLS **`8b94192ccead`** (8457B), AGENTS **`640c47b8088f`** (8358B).
- **Containers/models/image (unchanged):** pa/pro `claude-opus-4-7` thinking `high`; `openclaw-sandbox:capable-2026-06`; binds = gmail_helper + `<mailbox>-gmail.json:/creds/google.json` + google_helper (all ro) + auto `/workspace`.

## F26 proof (real agent turns)
- **Marie** (`openclaw agent --agent pa`, no `--deliver` → no Telegram noise): (1) read JS-only `quotes.toscrape.com/js/` → correct first quote (JS-injected); (2) `fill-and-shot` on `/login` → filled **username only**, surfaced the form fields, **did not enter the password, did not submit** (stage "pre-submit (NOT committed)"), screenshot `/workspace/_login_pre_submit.png`. 2 exec calls, 0 failures.
- **Charlotte** (`--agent pro`): JS read OK; **egress guard refused the host gateway `192.168.5.2:18789`** (exit 3); Soveren calendar regression green. 3 exec calls, 0 failures.
- **Regressions:** Gmail inbox reachable both mailboxes; google_helper calendar (Charlotte turn); LibreOffice 7.4.7.2 + python-docx/openpyxl import — all green. **Scratch clean:** Marie's browser turn left only the intended screenshot in `/workspace` (no new `.cache`/`.config`).

## Carried / new findings (see P6-34/35/36 for detail)
- **P6-35 (SECURITY, decision pending):** the sandbox reaches the host gateway control-plane (`192.168.5.2:18789`) + host SSH via lima's `host.lima.internal`. **Token-gating holds the boundary** (the gateway token is outside the container) and the browser egress-guard closes the SSRF vector, but it's a defence-in-depth gap. **Decision surfaced:** accept (token-gating + guard) vs harden the VM egress (DROP container→192.168.5.2). Recommended: accept now, harden as a scoped follow-up.
- **P6-36 (decision pending):** bootstrap injection truncates TOOLS.md ~52% (total budget `bootstrapTotalMaxChars=60000` across all bootstrap .md). Mitigated for the browser by a self-sufficient AGENTS.md line; the general fix (raise the cap ↔ token cost) folds into the chat-surface/context-hygiene cluster (P6-27/P6-30, now in 6c.7).
- **Browser-helper promotion (carry):** optionally promote to a single RO `/opt/browser_helper.py` bind at the next GUI session (needs the D20 restart) for a tamper-proof single source.
- **Clutter cleanup (carry):** stale `.cache`/`.config` in both workspaces (6c.2-era) — one-time delete, surfaced not auto-removed.

## Outstanding (carried, unchanged)
- **F28 cold-reboot proof + F45 + scheduling reliability (P6-29) + chat-surface hygiene (P6-27/P6-30) + cost/backup → 6c.7.**
- **PENDING-BATCH-APPLY (P6-11/12/14) + Charlotte avatar (P6-20) → 6c.7.**
- **Docs/Sheets *writes*** need an owned GCP project (P6-32) — relevant if 6c.5 doc output ever needs the Docs API (route via LibreOffice/Drive-export instead).
- **P6-24 sandbox-block drift** — pa+pro carry full sandbox blocks; re-sync on any `agents.defaults.sandbox` change (not triggered this session).
- **SR-1** docs-autopilot → 6d.

---
_Attachments for 6c.5: this closure + PHASE-6-DEVIATIONS-LOG.md (P6-34/35/36) + CAPABILITIES-ROADMAP-TO-6D (row 6c.5) + the 6c.5 opener. Older closures on demand._
