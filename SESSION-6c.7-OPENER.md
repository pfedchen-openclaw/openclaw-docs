# SESSION 6c.7 — Ops & reliability pass (+ browser breadth finish)
_Opener · generated at 6c.6 close (2026-06-13). The assistant-trio capabilities are now broad (web read, host real-browser, Google APIs, documents, email-draft). 6c.7 hardens **reliability/ops** so the stack survives reboots/updates and runs unattended — and finishes the residual browser breadth from 6c.6._

**Delivers:** a stack that (a) comes back cleanly after a cold reboot / `openclaw update`, (b) has the 6c.6 host-browser path made durable (P6-40 symlink fragility removed), (c) closes the deeper per-site pre-payment exit flows, and (d) advances the deferred ops cluster. **Exit =** verified cold-path recovery + a one-touch repair for the browser runtime dep + ≥2 real per-site pre-payment agent flows + progress on the host-egress/scheduling/chat-hygiene cluster.

## Stage 0 — Ledger + sanity (no mutating work until ALL-GREEN)
- Deviations log currency: confirm **P6-39 DONE** + **P6-40** present (log `8847796b236e`, 90255B at 6c.6 close). No staged rows pending lift.
- Read-only sanity batch: gateway healthz 200; pa+pro healthy `claude-opus-4-7`; both containers `capable-2026-06`; **A-7** live==last-good (`c48574d5ab10`/9928B at 6c.6 close); op SERVICE_ACCOUNT; subscription (no `ANTHROPIC_API_KEY`).
- **Verify the 6c.6 host-browser still works** (it depends on a manual symlink + running Chrome): `openclaw browser --json status`; if `playwright`/navigate fails, re-apply the **P6-40 symlink** then restart. Confirm `openclaw browser navigate https://example.com` ok and a real Marie `browser`-tool turn still drives an allowlisted site.
- Re-state standing items: SR-2 (ICS before any calendar write — still open); correspondence pattern-review before 6d; managed-remote browser deferred.

## Stage 1 — Browser durability + breadth finish (greenlight-gated)
- **P6-40 hardening (priority).** Make the `playwright-core` resolution survive `openclaw update`/reinstall: a one-touch repair script (re-create the symlink + `gateway restart`) wired into the runbook (and ideally the SR-1 autopilot). Decide: symlink vs `npm install playwright-core@1.59.1` into pkg-root `node_modules` vs a post-update hook. Verify by simulating (touch/rm the symlink → repair → navigate ok).
- **Deeper exit flows (real agent turns, pre-payment-safe).** Drive ≥2 of {BA fare search, Trainline Verona→Milano for given dates, Aman/Four Seasons availability for given dates} through Marie via the host `browser` tool to the **pre-payment / availability-results** state, surfaced and stopped. No login, no PII beyond search params. (Sites already proven to load real content at 6c.6.)
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
P6-40 symlink fragility · deeper per-site exit flows · warmed-profile logins · allowlist curation · SR-2 (ICS before calendar write) · correspondence review before 6d · managed-remote browser deferred · workspace cruft (P6-36) · signature-PNG (P6-33) · Docs/Sheets API writes (P6-32) · P6-35 · F28/F45/scheduling/chat-hygiene/cost/backup · PENDING-BATCH-APPLY (P6-11/12/14) · Charlotte avatar (P6-20).
