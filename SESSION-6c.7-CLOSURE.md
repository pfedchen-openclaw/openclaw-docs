# SESSION 6c.7 — Ops & reliability pass (browser durability + SSRF posture) — CLOSURE
_2026-06-13. Hardened the host-browser for unattended operation and, at Peter's direction, **renounced the domain allowlist** in favour of IP-class SSRF filtering — fixing the usability cliff (Trenitalia) and dissolving P6-41. New codes: **P6-42** (allowlist renunciation), **P6-43** (cold-path GUI-login gap), **P6-44** (complex-UI playbook), **P6-45** (payment-layer guardrail, forward), **P6-46** (long-turn → session-lock wedge). **P6-40 & P6-41 RESOLVED.** The session then expanded (Peter-led) into a **reliability + pre-CoS-planning + cost-accountability** arc — **P6-47** (agent unreliability diagnosis), **P6-48** (pre-CoS housekeeping roadmap + v7), **P6-49** (Andreas CTO/architect remit), **P6-50** (agent comms-hygiene review), **P6-51** (cost trace + hard no-unauthorised-test-spend rule) — captured in §8._

## §1 Outcome
The host browser is now both **durable** and **usable**:
- **P6-40 → durably repairable.** One-touch `scripts/browser-dep-repair.sh` restores the playwright-core symlink **and** the P6-42 guard patch after any `openclaw update`; tested by full simulated wipe; idempotent; wired into runbook §2.13 + symptom map.
- **P6-42 → domain allowlist renounced (Peter's decision).** After empirical analysis of the native guard, the enumerated `hostnameAllowlist` was emptied and a **one-token guard patch** makes an empty list **defer to the private-IP gate** rather than deny. Net posture: **all public sites reachable, all private/internal/loopback IPs blocked** — IP-class egress filtering, strictly more secure than the `dangerouslyAllowPrivateNetwork` escape hatch. This dissolved **P6-41** (no list ⇒ no off-list redirect hop) and fixed the Trenitalia failure.
- **Agent mental-model corrected.** pa+pro TOOLS.md/AGENTS.md rewritten: route by **injection-trust to logged-in sessions**, not a site list; never pre-refuse a public site from memory; "blocked by policy" ⇒ internal IP only.
- **F28 (P6-43):** cold-path autostart chain verified by config; decisive gap found — gui/502 + **auto-login OFF** ⇒ a cold reboot needs a GUI login to recover. Surfaced as a decision.
- **Complex-UI (P6-44):** live deep-booking turns stalled on controlled date pickers (UI robustness, not trust); playbook landed in both agents' docs; per-site recipes deferred to on-demand.
- **Payments (P6-45):** forward design constraint recorded — gate value at the card issuer, not in the agent.

## §2 What was done (arc)
- **Stage 0 ALL-GREEN.** Ledger current (`e380a8c90fee`/93932B; P6-39 RESOLVED, P6-40 RESOLVED-fragile, P6-41 OPEN). Sanity: gateway healthy (`openclaw health`; `18789/healthz`→200 `{ok:true}`; the opener's "healthz" lives on 18789, not the CDP 18800); pa+pro `claude-opus-4-7`; both containers `capable-2026-06`; **A-7 clean** `a3d90f697303`/9976B; op SERVICE_ACCOUNT; no `ANTHROPIC_API_KEY`. Host browser status OK, symlink intact, `navigate` ok, **real Marie turn** → example.com. Noted: gateway pid changed + containers up ~7h ⇒ a boot/login recovered the stack this morning (free F28 evidence); update 2026.6.6 available (pinned, not bumped).
- **Stage 1a (P6-40).** Built `browser-dep-repair.sh`; proved break→repair→verify by `unlink`+restart reproducing the exact failure, then repair (exit 0); idempotent no-op on re-run. Runbook §2.13 + symptom-map row added.
- **Stage 1b (P6-41→P6-42).** Read the guard (`cdp.helpers-*.js`/`ssrf-*.js`); confirmed the block = non-allowlisted-host rejection (google blocked, trainline passes), two-gate architecture, no universal wildcard, config all-or-nothing. **Peter, in conversation, chose to renounce the allowlist** (and raised the payments question → P6-45). Applied: `hostnameAllowlist=[]` + Gate-1 patch; restarted; verified open-web + SSRF-block; extended the repair script to re-apply the patch (full-wipe test passed); rewrote agent docs; F26 Marie→Trenitalia.
- **Stage 1c.** BA fare-search / Trainline retest **superseded** by P6-42 (the blocker is gone); deep pre-payment turns attempted live (Four Seasons, Booking.com) — reached the search/availability form, stalled on date-picker widgets → **P6-44** (UI robustness, not trust). Playbook added to docs.
- **Stage 2 (F28/P6-43).** Verified autostart config (no live reboot — would strand the box); found the auto-login gap; surfaced the decision.
- **Stage 4 close.** This closure + deviations additions + 6c.8 opener; shas re-anchored.

## §3 End-state (anchors)
- **openclaw.json:** **`3123a3807584`** (9437B) _(was `a3d90f697303`/9976B; only change = `browser.ssrfPolicy.hostnameAllowlist` → `[]`)_, perms `-rw------- staff`; **A-7 CLEAN** (last-good re-anchored byte-identical). `dangerouslyAllowPrivateNetwork` stays `false`.
- **Guard patch:** `dist/cdp.helpers-BWvLGafd.js` `63e4284773ad`/22170B (was `3d8cb5acf29b`/22171B); original at `…orig-6c7` (`: false`). **Update-fragile — re-applied by the repair script.**
- **Repair script:** `scripts/browser-dep-repair.sh` `30899bb59d4e`/5294B (exec).
- **Docs:** runbook `a50083f5bb73`/43534B (§2.13 + symptom map); pa TOOLS `493561049893` / AGENTS `6c304e685947`; pro TOOLS `e3893b8b6143` / AGENTS `b3cbf3e5f1e3`.
- **Deviations log:** **`1b45eebdf6aa`** (115031B) _(grew through the §8 post-close arc)_.
- **CLAUDE.md:** cost-discipline hard rule added to the autonomy posture ([P6-51]).
- **Claude Code memory added:** `security-design-philosophy`, `agent-comms-preferences`, `no-unauthorized-test-spend`.
- **Unchanged:** image `capable-2026-06`; pa/pro `claude-opus-4-7`; OpenClaw **2026.4.22 pinned** (2026.6.6 available, NOT bumped); gateway gui/502; Chrome 149 host.

## §4 F26 proof (real agent turns, `claude-opus-4-7`)
- **Marie, Stage 0:** Tier-1 `browser` → example.com H1 "Example Domain".
- **Marie, P6-42 (the headline):** first turn she *pre-refused* Trenitalia citing "SSRF" (stale 6c.6 allowlist instructions); forced to actually call the tool → reached real Trenitalia ("Trenitalia - Sito Ufficiale", "Biglietti"). After the doc rewrite, a **fresh natural-phrasing session** loaded Trenitalia and reported the real "CERCA" button — **no pre-refusal**. Proves both the runtime posture and the doc fix.
- **CLI posture battery:** example.com/Trenitalia/Wikipedia navigate; 192.168.1.1 / 169.254.169.254 / 127.0.0.1:18800 all refused; gateway↔CDP intact.
- **Deep pre-payment (P6-44/P6-46) — NOT achieved, carried:** Four Seasons stalled on the date-picker widget; Booking.com ran the full ~8.5 min and hit the **gateway turn timeout**, cascading to embedded-fallback → stale **session-lock** that wedged Marie's main session until a **gateway restart** cleared it (Marie → "OK" after). UI robustness + turn-duration, not a trust/SSRF block. The "one step past availability" proof moves to 6c.8 with the playbook + higher thinking + per-site recipe.

## §5 Decisions taken
- **Renounce the domain allowlist; IP-class SSRF filtering** (Peter, 6c.7) — P6-42. Unmaintainable list (broke Trenitalia); the allowlist never defended the value-exfiltration path anyway. More usable AND no weaker (private IPs still blocked). Reversible.
- **Gate value at the card issuer, not in the agent** (Peter, 6c.7) — P6-45 forward constraint.
- **Defer general complex-UI automation; bank playbook + on-demand per-site recipes** (6c.7) — P6-44.
- **No live cold reboot this session** (would strand the box; auto-login off) — verified by config + this-morning evidence; auto-login decision surfaced — P6-43.

## §6 Outstanding / carried (→ 6c.8)
> **Post-close reprioritisation (Peter):** a reliability diagnosis after this close ([P6-47]) found the agents are operationally unreliable (LLM usage cap hit → primary model rejected until 2026-07-01; gpt-4o fallback rate-limited; 6h heartbeat dead-zone; Charlotte's heartbeat disabled; Colima socket drops). **6c.8 was reprioritised to a dedicated reliability-hardening sprint** (see the rewritten 6c.8 opener). Housekeeping roadmap to 6d set in [P6-48]: 6c.8 reliability → empirical week → 6c.9 deviations audit (both logs) → 6c.10 canonical reconciliation + ARCHITECTURE v7 → 6d Andreas. The cold-path/booking/ops items below shift into 6c.8 (auto-login) / 6c.9.
- **P6-43 — auto-login decision** (enable for unattended cold-path vs documented manual login). **Recommend deciding at 6c.8.** Optionally then do a real Screen-Sharing reboot test.
- **P6-44 — per-site UI recipes** for the ~6 sites Peter uses; verify a deep pre-payment flow end-to-end with the new playbook + higher thinking (the Four Seasons/Booking date-picker step). **Not achieved live this session** — both deep turns stalled/timed out.
- **P6-46 — long-turn/gateway-timeout → stale session-lock** wedges the agent until a gateway restart; also exposes the below-tier `openai/gpt-4o` fallback. Mitigate via short flows (recipes) + revisit the timeout/fallback behaviour.
- **P6-45 — payments guardrail** when autonomy relaxes the L1 gate (6d/Andreas).
- **Browser breadth:** a clean BA fare-search turn; deep pre-payment depth on 1–2 sites via the playbook (the literal "one step past availability" proof).
- **Standing:** SR-2 (ICS before calendar write); correspondence review before 6d; managed-remote browser deferred; workspace cruft (P6-36); signature-PNG (P6-33); Docs/Sheets API writes (P6-32); P6-35 host-egress; scheduling (P6-29)/chat-hygiene (P6-27/30)/cost/backup; PENDING-BATCH-APPLY (P6-11/12/14) + Charlotte avatar (P6-20).
- **Pin:** 2026.6.6 update available — surface before any bump; after a bump, run `browser-dep-repair.sh`.

## §8 Post-close arc — reliability, pre-CoS planning, cost accountability (Peter-led)
After the core close, Peter pushed on the real day-to-day gap (agents unreliable) and the road to 6d. What this produced:
- **[P6-47] Agent unreliability diagnosed (evidenced from gateway logs).** 8am reports/heartbeats fail because: 🔴 **LLM usage cap hit** ("regain 2026-07-01") → primary model rejected; 🔴 `gpt-4o` fallback rate-limited; 🟠 heartbeat loop stalled **6h** today (KeepAlive ≠ healthy loop, no watchdog); 🟠 **Charlotte's heartbeat disabled**; 🟠 Colima socket drops ×12; 🟡 memory-file ENOENT; 🟡 zero observability. → **6c.8 reprioritised to a dedicated reliability sprint** (opener rewritten).
- **[P6-51] Cost trace + HARD RULE.** Per the Anthropic **Console (authoritative): 13 Jun ≈ $120**; my local reconstruction **overcounted ~3×** ($366) and shouldn't be cited — I led with the inflated figure and dramatised it, a mistake. The qualitative shape holds: **Marie-dominant, Opus-heavy, concentrated in _testing_** (this session's F26 turns + an 8.5-min Booking loop — **largely my own testing, which I own**), amplified by Marie's **52 MB session bloat** (also the [P6-46] wedge culprit). The $300 cap was reached (Peter raised → **$500**). **New binding rule (CLAUDE.md + memory): never spend materially on billed test-turns without explicit authorisation** — default to inspection/cheap-Sonnet/single-short-turn; authorise before anything >~$5 or Opus/loop/browser-heavy/batched.
- **[P6-48] Pre-CoS housekeeping roadmap:** **6c.8 reliability → ~1wk empirical Marie+Charlotte use → 6c.9 deviations audit (both logs) + comms-hygiene review → 6c.10 canonical reconciliation + ARCHITECTURE v7 → 6d Andreas.** Clarified (Peter): 6c.9 can run **during** the empirical week; only 6c.10 must follow it.
- **[P6-49] Andreas = CoS + CTO/architect engineering-steward** (code review, de-staling, refactor/update tracking, doc ownership) on a **propose-not-execute** boundary; formalise in v7.
- **[P6-50] Pre-CoS agent comms-hygiene review** — sweep Marie/Charlotte memory + persona files; seed fixes: no-substance-no-message, no signature in Telegram DMs, show-working-without-technical-detail. Quick wins foldable into 6c.8 once the cap is resolved (F26-verifiable).
- **Peter decisions taken:** renounce allowlist ([P6-42]); raise cap $300→$500; build the cost guardrail properly in 6c.8; 6c.9 in coming days, 6c.10 after the week. **Still pending Peter:** the LLM-cap *direction* (raise-vs-metered + tiering headroom) and [P6-43] auto-login.

## §7 Ledger — rows to apply at 6c.8 Stage 0
_Already applied to `PHASE-6-DEVIATIONS-LOG.md` this session: **P6-40 RESOLVED** + **P6-41 RESOLVED-by-dissolution** (appends); **P6-42 … P6-51** (new). No staged rows pending lift; the §6/§8 carries feed the 6c.8 opener directly._

---
_Attachments for 6c.8: this closure + PHASE-6-DEVIATIONS-LOG.md (`1b45eebdf6aa`) + CAPABILITIES-ROADMAP-TO-6D + the 6c.8 opener._
