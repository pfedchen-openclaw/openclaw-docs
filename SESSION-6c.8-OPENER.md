# SESSION 6c.8 — Agent reliability hardening (make Marie & Charlotte dependable)
_Opener · generated at 6c.7 close (2026-06-13), reprioritised after the post-close reliability diagnosis ([P6-47]). Marie & Charlotte have broad capability but are **operationally unreliable** — missed 8am reports, stalled heartbeats. This session fixes the infrastructure so they reliably do their jobs unattended, BEFORE any CoS work. **Not a capabilities session.**_

**Delivers:** agents that (a) have working model access, (b) beat on cadence with a watchdog so a stall is caught not silent, (c) both proactive (Charlotte re-enabled), (d) survive Colima socket drops, (e) surface their own failures to Peter. **Exit =** LLM access restored + healthy fallback · heartbeat watchdog live + verified by inducing a stall · Charlotte beating · ≥1 real 8am-style digest delivered end-to-end through a heartbeat · a missed-beat alert proven.

## Roadmap to 6d (set at 6c.7; see [P6-48])
**6c.8 reliability (this) → ~1 week empirical Marie+Charlotte use → 6c.9 pre-CoS deviations audit (both logs) + agent behaviour/comms-hygiene review ([P6-50]) → 6c.10 canonical reconciliation + ARCHITECTURE v7 (incl. Andreas's CTO/architect engineering-steward remit, [P6-49]) → 6d Andreas.** Do as much hardening/housekeeping as possible before CoS; the empirical week feeds 6c.9/6c.10. Don't finalise canonical docs early — 6c.10 is the designed reconciliation boundary precisely so it reflects post-empirical reality. **Quick-win comms fixes from [P6-50] (no signature in Telegram DMs; no-substance-no-message; show-working-without-technical-detail) are foldable into 6c.8 once the LLM cap is resolved so they can be F26-verified.**

## Stage 0 — Ledger + sanity (no mutating work until ALL-GREEN)
- Deviations log currency: confirm **P6-40/41 RESOLVED**, **P6-42..48** present (log `<re-anchor at open>` — was `036e7cf98cf7`/105078B + the P6-47/48 append at 6c.7 close). No staged rows pending lift.
- Sanity: `openclaw health` ok; `18789/healthz`→200 (NOT 18800 = CDP); pa+pro `claude-opus-4-7`; both containers `capable-2026-06`; **A-7** live==last-good (`3123a3807584`/9437B); op SERVICE_ACCOUNT; pin **2026.4.22** (2026.6.6 available — do NOT bump; after any bump run `browser-dep-repair.sh`).
- Browser posture (P6-42/40): `navigate https://www.trenitalia.com` ok + `navigate http://192.168.1.1/` refused; else run `scripts/browser-dep-repair.sh`.

## Stage 1 — Restore model access (BLOCKER; [P6-47] #1/#2)
- **LLM usage cap (Peter decision — surfaced).** `gateway.err.log`: *"reached your specified workspace API usage limits … regain 2026-07-01."* Primary model rejected all month. Resolve per Peter's call: raise the Anthropic workspace cap / move agents to **metered API billing** (no monthly cliff) / reduce consumption. **Nothing downstream works until this is fixed.** Verify with a real cheap agent turn that the primary model answers (not the fallback).
- **Fix the fallback chain.** Replace `agents.defaults.model.fallbacks` `openai/gpt-4o` (below-tier + rate-limited — security-audit WARN, [P6-46]) with a Claude fallback (e.g. `anthropic/claude-sonnet-4-6`) so a primary hiccup degrades gracefully. `openclaw config set` (gate-clear); verify a forced-fallback turn lands on Sonnet, not GPT-4o.

## Stage 2 — Heartbeat reliability ([P6-47] #3/#4)
- **Watchdog.** The beat loop stalled 6h today while the gateway process stayed up (`KeepAlive` ≠ healthy loop). Add a watchdog that detects a missed beat (last `[heartbeat] started` age > expected cadence) and restarts the loop/gateway, logging the event. **Verify by inducing a stall** (e.g. wedge a turn / session-lock per [P6-46]) → watchdog recovers it. Locate the heartbeat config first (not at `agents.*.heartbeat*`; `openclaw status` reports it — find the real key/command).
- **Re-enable Charlotte's heartbeat** (`pro` = disabled) with her documented lighter cadence (AGENTS.md / HEARTBEAT.md). Per-agent sandbox sync if needed (P6-24).
- **Cold-path / auto-login ([P6-43], Peter decision).** Decide auto-login (unattended cold-reboot recovery) vs documented manual login; if enabled, a real **Screen-Sharing reboot test (D20)**. Reliability-adjacent — fits here.

## Stage 3 — Sandbox + observability ([P6-47] #5/#6/#7)
- **Colima/Docker socket durability** — `Cannot connect to the Docker daemon` ×12. Confirm Colima KeepAlive/health; add a reconnect/health check so a socket drop self-heals.
- **Daily memory-file ENOENT** — ensure `workspace-p{a,o}/memory/YYYY-MM-DD.md` exists before the heartbeat save step (bootstrap/create-on-beat).
- **Observability** — a lightweight monitor that Telegrams Peter if a heartbeat is missed or the 8am digest didn't go out (silence becomes *known*). Reuse the parked security-audit launchd pattern (D24) if apt.

## Stage 4 — Close (three deliverables)
- DEVIATIONS-LOG.md additive (resolve/append P6-43/46/47; new codes for findings).
- SESSION-6c.8-CLOSURE.md.
- **SESSION-6c.9-OPENER.md** = the pre-CoS **deviations audit** ([P6-48]): cross-read both logs → one prioritised must-cover-before-Andreas backlog; carry the empirical-week findings.
- Re-anchor shas; record live `openclaw.json` sha; commit `_session-docs/`.

## Safety rails (whole session)
Reversible build steps run without prompting; surface irreversible/destructive (real sends, payments, **version-pin/D1 changes, auto-login enable, reboots, billing/cap changes**, depairing, destructive migrations) as decisions. `openclaw config set` is the gate-clear config path (jq/cp/stat on `openclaw.json` stay F21/F51-gated). Gateway **restart** SSH-safe; **install/bootstrap/bootout/reboot** need Screen-Sharing (D20). **Cost discipline ([P6-51], hard rule):** billed `openclaw agent` turns spend Peter's money — default to inspection/CLI verification; any F26 turn = single, short, cheapest sufficient model (Sonnet/Haiku, **never Opus for tests**), tight timeout, no loops/long-browser/batches; **explicit authorisation before any testing estimated >~$5 or using Opus/looping/browser-heavy/batched turns.** Stop at ~63% context for clean closure.

## Carry-forward (deferred from 6c.7 to 6c.9+, not lost)
Deep pre-payment proof + per-site UI recipes ([P6-44]) · BA fare-search · [P6-46] long-turn session-lock/fallback · [P6-45] payments guardrail (6d) · SR-2 (ICS before calendar write) · correspondence review before 6d · managed-remote browser deferred · workspace cruft ([P6-36]) · signature-PNG ([P6-33]) · Docs/Sheets API writes ([P6-32]) · [P6-35] host-egress · scheduling ([P6-29])/chat-hygiene ([P6-27]/[P6-30])/cost/backup · PENDING-BATCH-APPLY (P6-11/12/14) · Charlotte avatar ([P6-20]) · 2026.6.6 update (pinned — surface before bump).
