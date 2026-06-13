# SESSION 6c.7 — Ops & reliability pass (+ browser breadth finish)
_Opener · generated at 6c.6 close (2026-06-13). The assistant-trio capabilities are now broad (web read, host real-browser, Google APIs, documents, email-draft). 6c.7 hardens **reliability/ops** so the stack survives reboots/updates and runs unattended — and finishes the residual browser breadth from 6c.6._

**Delivers:** a stack that (a) comes back cleanly after a cold reboot / `openclaw update`, (b) has the 6c.6 host-browser path made durable (P6-40 symlink fragility removed), (c) closes the deeper per-site pre-payment exit flows, and (d) advances the deferred ops cluster. **Exit =** verified cold-path recovery + a one-touch repair for the browser runtime dep + ≥2 real per-site pre-payment agent flows + progress on the host-egress/scheduling/chat-hygiene cluster.

## Stage 0 — Ledger + sanity (no mutating work until ALL-GREEN)
- Deviations log currency: confirm **P6-39 DONE** + **P6-40** + **P6-41** present (log `e380a8c90fee`, 93932B at 6c.6 close). No staged rows pending lift.
- Read-only sanity batch: gateway healthz 200; pa+pro healthy `claude-opus-4-7`; both containers `capable-2026-06`; **A-7** live==last-good (`a3d90f697303`/9976B at 6c.6 close); op SERVICE_ACCOUNT; subscription (no `ANTHROPIC_API_KEY`).
- **Verify the 6c.6 host-browser still works** (it depends on a manual symlink + running Chrome): `openclaw browser --json status`; if `playwright`/navigate fails, re-apply the **P6-40 symlink** then restart. Confirm `openclaw browser navigate https://example.com` ok and a real Marie `browser`-tool turn still drives an allowlisted site.
- Re-state standing items: SR-2 (ICS before any calendar write — still open); correspondence pattern-review before 6d; managed-remote browser deferred.

## Stage 1 — Browser durability + breadth finish (greenlight-gated)
- **P6-40 hardening (priority).** Make the `playwright-core` resolution survive `openclaw update`/reinstall: a one-touch repair script (re-create the symlink + `gateway restart`) wired into the runbook (and ideally the SR-1 autopilot). Decide: symlink vs `npm install playwright-core@1.59.1` into pkg-root `node_modules` vs a post-update hook. Verify by simulating (touch/rm the symlink → repair → navigate ok).
- **P6-41 (priority) — hrefless SPA *submit* navigation is SSRF-blocked.** At 6c.6, Booking/Aman/Four Seasons reached real availability/results via agent turns (direct-URL pattern), but clicking an hrefless React submit/"Continue" button that triggers client-side navigation is rejected by the browser SSRF guard ("requires IP-literal …"), even on an allowlisted host (Trainline). Click-through on real `<a href>` links to allowlisted hosts works (verified). **Fix (A-12, empirical):** add a tool/policy option to permit click-driven navigation when the current page's host is allowlisted (same-origin SPA nav), or relax the redirect-chain check to the allowlisted landing host — without weakening cross-origin enforcement. Then drive **1 booking one step past availability** (e.g. Four Seasons "Select Room" → guest-details *pre-payment*) as the proof.
- **Deeper exit flows (real agent turns, pre-payment-safe) — mostly done at 6c.6.** Remaining: a **BA fare-search** turn; **Trainline** dated-departure retest via a different date/route (6c.6's empty result was a Trainline-UK Italian-internal inventory gap, not our code). No login, no PII beyond search params.
- **Warmed-profile logins** — only if a target flow is login-walled: do the manual logins in a **Screen-Sharing** session into the host Chrome profile (`browser-login`); never auto-enter credentials.
- **Allowlist curation** — extend `browser.ssrfPolicy.hostnameAllowlist` for any new reputable site (and professional domains for Charlotte) via `openclaw config set` (gate-clear).

## Stage 2 — Cold-path & update resilience (greenlight-gated)
- **F28 cold reboot.** Verify (or script) clean recovery after a full Mini reboot: gateway LaunchAgent RunAtLoad/KeepAlive, Colima auto-start, containers + host Chrome come back, healthz 200, both agents reachable. Note any GUI-session (D20) dependency for the headful browser on a headless boot.
- **`openclaw update` posture (A-12 / pin).** 2026.4.22 is pinned — do NOT bump without surfacing. Document what an update would wipe (P6-40 symlink, bundled deps) and the re-apply path. Surface any version-pin change as a decision.

## Stage 3 — Ops cluster (greenlight-gated; pick by impact)
- **P6-35 host-egress hardening** (sandbox→host control-plane reachability) — revisit now that the browser path is gateway-mediated (not container→host).
- **Scheduling reliability (P6-29)**, **chat-surface hygiene (P6-27/P6-30)**, **cost guardrail + backups** (`openclaw backup`).
- **PENDING-BATCH-APPLY (P6-11/12/14)** + **Charlotte avatar (P6-20)** — apply if low-risk.

## Stage 4 — Close (three deliverables)
- DEVIATIONS-LOG.md additive (new P6 codes for findings; §7 ledger if any staged rows).
- SESSION-6c.7-CLOSURE.md.
- SESSION-6c.8-OPENER.md.
- Re-anchor shas; record live `openclaw.json` sha; commit `_session-docs/`.

## Safety rails (whole session)
Reversible build steps run without prompting; surface irreversible/destructive (real sends, payments, **version-pin/D1 changes**, depairing, destructive migrations) as decisions. `openclaw config set` is the gate-clear config path (jq/cp/stat on `openclaw.json` stay F21/F51-gated). Gateway **restart** is SSH-safe; **install/bootstrap/bootout** need Screen-Sharing (D20). Stop at ~63% context for clean closure.

## Carry-forward (full list in 6c.6 closure §6)
P6-40 symlink fragility · **P6-41 SPA-submit SSRF block** · deeper checkout-click depth (post-P6-41) · BA fare-search turn · warmed-profile logins · allowlist curation · SR-2 (ICS before calendar write) · correspondence review before 6d · managed-remote browser deferred · workspace cruft (P6-36) · signature-PNG (P6-33) · Docs/Sheets API writes (P6-32) · P6-35 · F28/F45/scheduling/chat-hygiene/cost/backup · PENDING-BATCH-APPLY (P6-11/12/14) · Charlotte avatar (P6-20).
