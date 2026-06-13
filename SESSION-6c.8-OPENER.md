# SESSION 6c.8 — Cold-path decision, deep-booking proof & ops cluster
_Opener · generated at 6c.7 close (2026-06-13). 6c.7 made the host browser durable (P6-40 repair script) and **renounced the domain allowlist for IP-class SSRF filtering** (P6-42) — open web works, internal IPs blocked, P6-41 dissolved. 6c.8 closes the **cold-path decision** (P6-43 auto-login), proves a **deep pre-payment booking flow** with the new UI playbook (P6-44), and advances the **ops cluster**._

**Delivers:** (a) a decided + (if chosen) implemented cold-reboot recovery posture; (b) ≥1 real agent booking driven one step **past availability to a pre-payment screen** via the P6-44 playbook; (c) a BA fare-search turn; (d) progress on the host-egress/scheduling/chat-hygiene/cost-backup cluster. **Exit =** auto-login decided (+ verified if enabled) · 1 deep pre-payment flow proven end-to-end · ≥1 ops-cluster item closed.

## Stage 0 — Ledger + sanity (no mutating work until ALL-GREEN)
- Deviations log currency: confirm **P6-40/41 RESOLVED**, **P6-42/43/44/45/46** present (log `036e7cf98cf7`, 105078B at 6c.7 close). No staged rows pending lift.
- Sanity batch: `openclaw health` ok; `18789/healthz`→200 (NOT 18800 — that's the CDP port); pa+pro `claude-opus-4-7`; both containers `capable-2026-06`; **A-7** live==last-good (`3123a3807584`/9437B); op SERVICE_ACCOUNT; no `ANTHROPIC_API_KEY`; pin still **2026.4.22** (2026.6.6 available — do NOT bump without surfacing).
- **Browser posture check (P6-42/P6-40):** `openclaw browser navigate https://www.trenitalia.com` → ok (open web); `…navigate http://192.168.1.1/` → **refused** (SSRF intact); guard sha `63e4284773ad`. If either fails (e.g. an update wiped it), run **`~/.openclaw/scripts/browser-dep-repair.sh`** (restores symlink + guard patch, restarts, verifies). 
- Re-state standing: SR-2 (ICS before any calendar write); correspondence review before 6d; managed-remote browser deferred.

## Stage 1 — Cold-path decision (P6-43, greenlight-gated)
- **Auto-login decision (Peter's call — security tradeoff).** Enable auto-login for `openclaw` → cold reboot self-recovers unattended (gateway/Colima/Chrome are gui/502); **but** weakens at-rest security (boots into a logged-in session; FileVault interaction). Alternative: keep auto-login off and **document** the "log in via Screen Sharing after a reboot" step as the recovery procedure. **Surface as a decision; do not flip unilaterally.**
- If auto-login is enabled: do a **real reboot test from a Screen-Sharing GUI terminal (D20)** — verify gateway healthz 200, both containers back (`RestartPolicy=no` ⇒ gateway brings them up), host Chrome relaunches, a real agent turn works. If kept manual: write the procedure into runbook §2.1.

## Stage 2 — Deep-booking proof + breadth (P6-44, greenlight-gated)
- **One real pre-payment flow, end-to-end.** Using the **P6-44 playbook** (type-the-date not click-the-calendar; encode search in the URL + `navigate`; `evaluate`+event-dispatch escape hatch; snapshot→act→verify) and **higher thinking** (`--thinking high`), drive a booking (Four Seasons Milano or a Booking.com property) from availability **through to the guest-details/pre-payment screen**, STOP, screenshot, surface. No PII/payment entered. This is the literal "one step past availability" proof carried since 6c.6.
- **Per-site recipe.** Capture the known-good sequence (or direct-URL template) for that site as a reusable snippet (the on-demand recipe pattern, not a general solver).
- **BA fare-search turn** (breadth); optional Trainline dated-departure retest via a different route/date.

## Stage 3 — Ops cluster (greenlight-gated; pick ≥1 by impact)
- **Cost guardrail + backups** (`openclaw backup`; verify the op-backup/vault-backup jobs fire) — good unattended-reliability fit.
- **P6-35 host-egress hardening**; **scheduling reliability (P6-29)**; **chat-surface hygiene (P6-27/P6-30)**.
- **PENDING-BATCH-APPLY (P6-11/12/14)** + **Charlotte avatar (P6-20)** — apply if low-risk.
- **P6-45 (forward):** if touching payments/autonomy design, record the card-issuer guardrail concretely (virtual-card caps + MCC blocks) — no live payment wiring yet.

## Stage 4 — Close (three deliverables)
- DEVIATIONS-LOG.md additive (new P6 codes; append updates to P6-43 etc.).
- SESSION-6c.8-CLOSURE.md.
- SESSION-6c.9-OPENER.md.
- Re-anchor shas; record live `openclaw.json` sha; commit `_session-docs/`.

## Safety rails (whole session)
Reversible build steps run without prompting; surface irreversible/destructive (real sends, payments, **version-pin/D1 changes, auto-login enable, reboots**, depairing, destructive migrations) as decisions. `openclaw config set` is the gate-clear config path (jq/cp/stat on `openclaw.json` stay F21/F51-gated). Gateway **restart** is SSH-safe; **install/bootstrap/bootout/reboot** need Screen-Sharing (D20). After any `openclaw update`: run `browser-dep-repair.sh`. Stop at ~63% context for clean closure.

## Carry-forward (full list in 6c.7 closure §6)
**P6-43 auto-login decision** · **P6-44 per-site recipes + deep pre-payment proof** · **P6-46 long-turn session-lock / gpt-4o fallback** · BA fare-search · P6-45 payments guardrail (6d) · warmed-profile logins (Screen-Sharing) · SR-2 · correspondence review before 6d · managed-remote browser deferred · workspace cruft (P6-36) · signature-PNG (P6-33) · Docs/Sheets API writes (P6-32) · P6-35 · scheduling/chat-hygiene/cost/backup · PENDING-BATCH-APPLY (P6-11/12/14) · Charlotte avatar (P6-20) · 2026.6.6 update (pinned — surface before bump).
