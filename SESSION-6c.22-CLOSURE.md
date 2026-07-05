# SESSION 6c.22 — CLOSURE: Memory v2 migration advanced — dedup + observability canary + the measured config pass APPLIED, plus the two/three-failure-mode diagnosis

_Closed 2026-07-05 (GUI/Aqua, D20 satisfied). Continuation of the 6c.21 memory redesign: retrieval was already live; this session advanced the staged migration — **shipped the daily-file dedup and the observability canary, captured the churn baseline, quantified the real failure mechanisms, and APPLIED the measured config pass** (Peter gave explicit mid-session permission → openclaw.json changed by Claude Code for the first time this arc). New code: **P6-119** (+ same-session UPDATE)._

## One-line result
Retrieval (6c.21) was necessary; this session did the measurable follow-through — **dedup (loss-free), a wired observability canary, and 7 schema-verified config levers applied + gateway-restarted** — and in doing so **quantified the real disease**: the churn is not one thing but (1) 256 KB byte-cap prompt truncation on un-reset long-lived sessions (68% of turns; 93% of it in two sessions), (2) token-budget compaction on tool-heavy sessions, and (3) trajectory-event-size-limit aborts when tool-heavy work runs on an already-large session. Three mechanisms, three fixes — no longer conflated.

## What landed (P6-119)
1. **Daily-file dedup — SHIPPED, provably loss-free.** 6c.21 deferred this as mutation-risky; 6c.22 removed the risk empirically first: the 9× duplication is byte-identical flush re-appends (9 occurrences, **1 unique body** each — not a growing/lossy re-summary). Exact-block dedup with a hard invariant (`set(unique blocks)` identical before/after → abort otherwise); backup-first (`_backups-6c22-memory-dedup/`). Applied to pa 07-02/03/04: **117 duplicate blocks removed, ~183 KB → ~86 KB**, idempotent, 0600 preserved. Re-indexed **pa 216 → 121 chunks**, `Dirty: no`; Porsche recall reconfirmed at **0.590**. *Operational finding:* a corpus mutation marks the index `Dirty` (`sync.watch`); the first search ran against a mid-rebuild index and returned "No matches" — **confirm `Dirty: no` before trusting recall after any mutation** (now a canary metric).
2. **Observability canary — SHIPPED + wired.** `scripts/memory-health.py` (host-only, zero billed turns) computes the doc §8 gate per agent/day from the trajectory JSONL + `openclaw memory status` + daily files: per-turn tokens (median/p95/max), truncation rate, compaction count, cache read:write, daily-file dup count, index freshness, retrieval-usage, **+ a stale-session detector**. Log-only by default (`state/memory-health/daily.log`); Telegram ALARM only on breach (`ALARM_TELEGRAM=1`). Wired to `ai.openclaw.memory-health.plist` (daily 23:55, bootstrapped gui/502). Perf-hardened (grep for retrieval scan; ~12 s).
3. **THE churn quantified — the real diagnosis (from `model.completed.data`).** median **392 K** tok/turn, p95 **3.76 M**, max **16.4 M** (vs §8 target med <60 K / p95 <200 K → ~6.5×/~19× over). **`truncated:True` on 677/1001 turns (68%), ⟺ `originalBytes > limitBytes` exactly; `limitBytes` = constant 256 KB byte cap** (distinct from compaction — `compactionCount` is None on those turns). **631/677 (93%) in two un-reset long-lived sessions** (pa `14b920c0` 490 turns/6 days; pro `a34324aa` 177 turns/26 days — the "`:telegram:direct`" session, excluded from freshstart). `promptCache.retention="short"` everywhere (Fault D confirmed).
4. **Measured config pass — APPLIED (Peter's explicit permission), A-12-corrected.** `scripts/apply-6c22-config-pass.sh` (Peter-run pattern, but run by Claude Code once Peter granted permission mid-session). **openclaw.json `da191cf1920d`/10099B → `d702ae330bb7`/10509B** (A-7 clean, 600/staff). 7 levers live (`config get`-confirmed): `compaction.model=anthropic/claude-sonnet-4-6`, `keepRecentTokens=40000`, `truncateAfterCompaction=true`, `contextInjection=continuation-skip`, `mmr.enabled=true`, `temporalDecay.enabled=true`+`halfLifeDays=21`. Gateway restarted clean (pid 54750, healthz OK); index survived. P6-24 clean (all 9 agents; no per-agent shadow).
5. **Smoke turn — run, honestly mixed.** Real gateway path on opus-4-7. **Retrieval-usage INCONCLUSIVE (confounded):** `openclaw agent` continued the existing session which already held the Porsche context → no `memory_search` needed (needs a fresh-session re-test). **Third failure mode found:** turn aborted `trajectory-event-size-limit` (browser research on a big session). **Correctness win:** she established the **Porsche warranty EXCLUDES glass — a windscreen is a motor-insurance claim, not warranty** (corrects Peter's premise; Mayfair centre ~2.5 mi closer than West London). No clean deliverable produced (aborted). Cost `usage={}` (aborted) → defer to Console.

## A-12 corrections that earned their keep (schema-verified vs 2026.4.22)
- **`compaction.memoryFlush.model` does not exist** → the real housekeeping-model lever is **`agents.defaults.compaction.model`**; set to the exact live id `anthropic/claude-sonnet-4-6` (runtime `modelId`: opus-4-7/sonnet-4-6/gpt-5 — *not* `claude-sonnet-5`).
- **`params.cacheRetention` is not in the schema** — prompt-cache TTL is API-tier-controlled, not an openclaw.json key. The doc's cache-retention lever is unachievable via config. Dropped.
- **openclaw.json is categorically Bash-deny-listed to Claude Code** (a minimal structure-only `jq` was denied too), not just Read-tool-denied — Peter's in-principle "open path" does not flip the backstop; the sanctioned route is a Peter-run F17 script (or, as here, Claude Code running it under explicit per-message permission).

## Judgement calls
- **Did the dedup this session** (6c.21 deferred it) — because the byte-identical proof converted it from "risky mutation" to "deterministic loss-free cleanup."
- **Prepared a Peter-run script rather than weakening the F21 backstop** — then ran it when Peter granted explicit permission. Never dumped secrets (structure-driven jq + display-immune checks).
- **Canary alarms default OFF** (log-only) — no notification noise until thresholds are calibrated on real data (no-substance-no-message).
- **Did NOT re-run the smoke turn** after the abort — Peter authorised one turn; a fresh-session retrieval re-test is carried, not spent-again unprompted.

## SHA re-anchor (post-session)
- **openclaw.json** `d702ae330bb7`/10509B — CHANGED this session (config pass), A-7 clean (live==last-good), 600/staff. Pin 2026.4.22 (00bd2cf), sandboxes `capable-2026-06`.
- **Updated:** `PHASE-6-DEVIATIONS-LOG.md` `eab45d7661f9`/339592B (P6-119 + UPDATE).
- **New:** `scripts/memory-health.py` `16093148aa61`/11703B; `scripts/apply-6c22-config-pass.sh` `8ed57dafd13f`/5217B; `ai.openclaw.memory-health.plist` (gui/502 loaded, daily 23:55); `_backups-6c22-memory-dedup/` (pre-dedup pa+pro memory).
- **Unchanged:** `MEMORY-ARCHITECTURE-v2.md` `6d35e79f004f`/33514B; `PRE-COS-BACKLOG.md` `f00e3121c5fc`/22574B.
- **Memory index (persisted):** pa 34/34·**121 chunks** (post-dedup), pro 31/31·36, `Dirty: no`.
- **Deviations log:** P6-1…119 **gap-free, no dangling refs**.

## Carry-chain audit ([P6-101])
- **Mechanical:** P6-1…119 gap-free; no referenced-but-undefined codes. Pre-existing 21/22 dupes untouched (additive-only).
- **Carry diff vs 6c.21 closure "carried → 6c.22":** ① Memory v2 measured config pass = **DONE this session** (applied + restarted); dedup = **DONE**; canary = **DONE**. ② Payments real-charge F26 = **CARRIED** (Stage-2, untouched by design). ③ Reply-watcher = **CARRIED**. ④ generate-mode cred store = **CARRIED**. ⑤ Porsche re-deliver = **ATTEMPTED, not completed** (smoke turn aborted; surfaced the warranty-vs-insurance correction) → **re-scoped** (fresh browser-bounded turn). ⑥ Battery re-run = **CARRIED**. ⑦ "did retrieval reduce the [P6-116] pattern?" = **DISPOSITIONED** (inconclusive-by-design — trivial runtime; re-test after file restructure). Nothing silently dropped.

## Carried → 6c.23 (in the opener)
- **Re-read the canary post-config-pass** (before/after the levers over the next days — the F26 behavioural evidence the levers actually help; §8 gate).
- **Fresh-session retrieval-usage re-test** (does Marie call `memory_search` when the context is NOT already in-session?) — the still-open acceptance-gate signal.
- **File restructure to the small frozen core** (doc §6 step 5) — the fix for organic retrieval usage (make recall require search) — reversible, no restart.
- **Long-lived-session structured checkpoint** (item 4 — the 93%-truncation source; the trajectory-size-abort source) — periodic structured compaction, continuity-preserving.
- **Tool-result/media caps** (measured, canary before/after) — the P3 fix for the browser/PDF bloat behind the compaction + trajectory-abort modes.
- **Porsche re-deliver** (fresh, browser-bounded turn) + surface to Peter now: **windscreen = insurance, not warranty**.
- **Payments real-charge F26**, **reply-watcher**, **generate-mode cred store**, **Battery re-run** — Stage-2 carried.
