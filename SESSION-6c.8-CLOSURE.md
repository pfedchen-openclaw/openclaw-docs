# SESSION 6c.8 — Agent reliability hardening (make Marie & Charlotte dependable) — CLOSURE
_2026-06-13. A reliability sprint, not capabilities. Restored model access, fixed the fallback + tiering, built and proved a heartbeat watchdog, re-enabled Charlotte, killed the memory-ENOENT, and proved the digest pipeline end-to-end. **All five exit criteria met.** New codes **P6-53…P6-57**; **P6-43 CLOSED**, **P6-46/47 resolved/appended**. The headline surprise: the model-access blocker was already cleared by Peter's $500 cap-raise — confirmed at the runtime, not just the Console._

## §1 Outcome — all five exit criteria met
1. **LLM access restored + healthy fallback ✅.** Peter raised the production workspace cap $300→**$500**; confirmed at the runtime by one cheap Sonnet `capability model run --gateway` → `provider=anthropic, model=claude-sonnet-4-6`, **zero failover** (the 16:41/17:27 cooldown had cleared). Fallback chain reworked to **`["openai/gpt-5"]`** — a universal cross-provider backup under any Anthropic model (Peter's decision, [P6-58]); tiering untouched, non-sticky, loud-flagged.
2. **Heartbeat watchdog live + stall-verified ✅.** `heartbeat-watchdog.sh` + LaunchAgent (bootstrapped into gui/502); induced-stall test → real `gateway restart` + real Telegram alert (Msg ID 187) + healthy recovery; cooldown guard prevents restart-loops.
3. **Charlotte beating ✅.** `pro` heartbeat re-enabled (4h ≈ her 3–4 daytime sweeps); Marie preserved at 30m (both given explicit blocks to dodge the "only-those-agents-beat" trap); both tiered to Sonnet.
4. **≥1 real digest through the pipeline ✅.** Synthetic heartbeat trigger correctly **refused by Marie's injection defence** ([P6-55]); proven instead via Peter's authorised Telegram trigger → Marie delivered *"Morning digest — Sun 14 Jun (6c.8 test run)"*.
5. **Missed-beat alert proven ✅** (the watchdog's Telegram alert, Msg ID 187).

Also banked: **memory-ENOENT killed** (today's `memory/<date>.md` bootstrapped each watchdog run — root cause was the 07:27 beat reading the file before it's written); **cold-path recovery documented** (runbook §2.1, [P6-43] closed); **tiering** (routine beats → Sonnet, the big steady-state cost cut).

## §2 What was done
- **Stage 0 ALL-GREEN.** pin 2026.4.22 (2026.6.6 available, not bumped) · `health` ok · `18789/healthz`→200 · op SERVICE_ACCOUNT · A-7 clean (`3123a3807584`) · both containers `capable-2026-06` · pa/pro `claude-opus-4-7` · browser public-ok/private-blocked · deviations log re-anchored `7b93e7e4b9ff`/118676B (P6-40/41 RESOLVED, P6-42…52 present). Diagnosed the whole reliability picture **by inspection, zero spend**: cap still rejecting at 17:27, gpt-4o fallback rate-limited, ~4h heartbeat dead-zone, pro disabled, stale running task.
- **Stage 1.** Cap proven clear (Peter's raise); fallback → sonnet-4-6; tiering via the heartbeat `model` field. (`usage-cost` reads $0 — doesn't capture embedded spend → Console authoritative; recorded.)
- **Stage 2.** Found the heartbeat mechanism (`agents.list[].heartbeat`, not in any `heartbeat` key; "only-those-agents-beat" gotcha — [P6-53]); both pa+pro explicit Sonnet-tiered blocks; gateway restart over SSH (clean); watchdog built, **re-based onto the `gateway.log` `[heartbeat] started` signal** after finding `system heartbeat last` returns `null` post-restart ([P6-54]); proven by induced stall + cooldown-skip; cold-path doc.
- **Stage 3.** Memory bootstrap folded into the watchdog; missed-beat Telegram alert = the observability lever (proven). Colima `KeepAlive=false` found → carried ([P6-57], needs a socket-health-check not just KeepAlive).
- **Stage 4 close.** This closure + deviations P6-53…57 (+ P6-43/46/47 appends) + 6c.9 opener; A-7 re-anchored; shas recorded; docs committed.

## §3 End-state (anchors)
- **openclaw.json:** **`6554d79e955d`** (9782B) _(was `3123a3807584`/9437B; changes = `model.fallbacks`→`["openai/gpt-5"]` + `agents.defaults.models` gains `openai/gpt-5` + `agents.list[0|1].heartbeat` blocks)_, `-rw------- staff`; **A-7 CLEAN** (last-good re-anchored byte-identical). `dangerouslyAllowPrivateNetwork` unchanged `false`.
- **Watchdog:** `scripts/heartbeat-watchdog.sh` `5454724a7619`/4925B (exec; incl. the [P6-58] fallback loud-flag); `~/Library/LaunchAgents/ai.openclaw.heartbeat-watchdog.plist` `376b21a02610`/807B (loaded gui/502, StartInterval 900, RunAtLoad).
- **Runbook:** `99c13d43c1c9`/44866B (§2.1 cold-path note).
- **Deviations log:** **`7e45ca1bb30d`** (128074B).
- **Unchanged:** image `capable-2026-06`; pa/pro interactive `claude-opus-4-7`; OpenClaw **2026.4.22 pinned**; gateway gui/502; browser SSRF posture (P6-42).

## §4 F26 proof (real, billed turns — Peter-authorised, Sonnet, single)
- **Access confirm:** `capability model run --gateway --model …sonnet-4-6` → `OK`, `provider=anthropic`, zero failover. ~nil.
- **Digest pipeline:** synthetic heartbeat trigger refused (injection defence — [P6-55]); Peter's authorised Telegram trigger → real digest delivered (Gosuslugi greeting auto-archived; Gymnasium One invoice due 25 Jun flagged).
- **Watchdog:** induced stall → real restart + Telegram alert (Msg ID 187) + recovery; healthy/cooldown paths verified by inspection.

## §5 Decisions taken
- **Universal cross-provider fallback → `openai/gpt-5`** (Peter, 6c.8 — [P6-58]). Drops the below-tier gpt-4o; the interim Anthropic-only `[sonnet]` left Sonnet-primary turns (heartbeats/defaults) with no backup — Peter caught it. gpt-5 backs *any* Anthropic model, survives a pan-Anthropic outage, stays failover-only (tiering intact), non-sticky, and loud-flagged via the watchdog.
- **Both agents get explicit heartbeat blocks** (avoid the only-those-beat trap), Sonnet-tiered.
- **Watchdog liveness = gateway.log, not `system heartbeat last`** (null post-restart).
- **Dev/test workspace split → deferred to 6c.9** (cap-raise alone unblocked; not needed now).
- **Cold-path: accept manual login** (documented; [P6-43] implemented).

## §6 Outstanding / carried (→ 6c.9, see [P6-57])
- Colima socket-health durability (KeepAlive=false found; needs a docker-info health-check, not just KeepAlive).
- Marie's **92 MB** session compaction ([P6-51]/[P6-46]).
- Daily spend visibility (usage-cost=$0; Console-API/token-sum design).
- Dev/test workspace+key ([P6-51] ring-fence) — Peter's Console action when convenient.
- **Standing (unchanged):** [P6-50] comms-hygiene (now F26-able — cap resolved); [P6-44] per-site recipes; [P6-45] payments (6d); SR-2 ICS-before-calendar; [P6-32] Docs/Sheets writes; [P6-33] signature-PNG; [P6-36] workspace cruft; PENDING-BATCH-APPLY (P6-11/12/14); [P6-20] Charlotte avatar; [P6-52] Maps/reservations (capability session). 2026.6.6 update pinned (surface before bump; run `browser-dep-repair.sh` after).
- **Empirical week now starts** (Marie+Charlotte real use) — feeds 6c.9.

## §7 Ledger — rows applied this session
_Applied to `PHASE-6-DEVIATIONS-LOG.md`: **P6-43 CLOSED** (append), **P6-46/47 appended** (P6-47 RESOLVED), **P6-53…P6-57 new**. No staged rows pending lift; §6 carries feed the 6c.9 opener._

---
_Attachments for 6c.9: this closure + PHASE-6-DEVIATIONS-LOG.md (`7e45ca1bb30d`) + the frozen DEVIATIONS-LOG.md + CAPABILITIES-ROADMAP-TO-6D + the 6c.9 opener._
