# SESSION 6c.21 — CLOSURE: Memory/Context Architecture v2 designed; retrieval turned ON + verified

_Closed 2026-07-05 (GUI/Aqua, D20 satisfied). The LEAD — the commissioned foundational memory redesign ([P6-116]) — delivered: v2 design doc + full cited best-practice research + the single highest-value migration step **shipped and F26-verified** (retrieval). Two of the review's premises corrected empirically. openclaw.json **UNCHANGED** all session (`da191cf1920d`/10099B, A-7 clean) — no config thrash, deliberately. New code: **P6-118**._

## The one-line result
OpenClaw already ships the retrieval/consolidation memory stack the review said was missing — **it was switched off**. v2 is therefore *activation + correct wiring + file restructuring of machinery we own*, not net-new engineering. Retrieval is now **on and proven**; the rest is a costed, staged, Andreas-owned migration.

## What landed (P6-118)
1. **v2 design doc — `MEMORY-ARCHITECTURE-v2.md` (`6d35e79f004f`/33514B).** Six principles → concrete OpenClaw wiring; a validation matrix (every past incident [P6-93/97/98/102/103/108/115/116] → the mechanism that kills it); a 7-step staged/measured migration (F17/P6-24); a measurable acceptance gate; the real config surface (secret-free via `config schema`/`get`). Supersedes `MEMORY-CONTEXT-ARCHITECTURE-REVIEW.md` as the build spec.
2. **External best-practice research — done properly, two primary-sourced tracks, cited in §2.** *Anthropic:* two-tier index+on-demand (Claude Code's own model), cache economics (read 0.1× vs write 1.25×/2×, prefix stable→volatile), context-editing (−84% tokens)/compaction/memory role-split, "ASSUME INTERRUPTION", 25K tool caps. *Academic:* CoALA four-store split (+procedural), MemGPT pin-core/page-rest + save-then-evict, Generative Agents recency+importance+relevance + reflection, Mem0/LangMem **idempotent ADD/UPDATE/DELETE/NOOP** (the exact 9×-dup fix), Zep supersession, structured-running-state over prose. Every mechanism maps 1:1 to an OpenClaw feature that was off/unset here.
3. **Retrieval turned ON — migration Step 1, shipped + verified (reversible, no restart, no config).** Provider was ready all along (`memory status --deep`: `Embeddings/Vector/FTS: ready`); the index had simply never been built. `openclaw memory index --force` → **pa 34/34 files·216 chunks, pro 31/31·36, `Dirty: no`** (persists across restarts; `sync.watch`/`onSessionStart` keep it fresh). **F26-by-inspection (zero billed turns):** the query "Porsche windscreen warranty write-up" — which returned **`count:0`** on the [P6-116] failure day (→ Marie rebuilt from scratch → lost to compaction → Peter got nothing) — now returns the Porsche memory at **0.667 relevance**. The [P6-116] rebuild-from-scratch mechanism is killed at the retrieval layer.
4. **Andreas inheritance wired (Peter's 2nd instruction).** Full v2 + every not-yet-implemented item folded into `PRE-COS-BACKLOG.md` §A.4 (11-item Andreas backlog with provenance to the doc + cited research). The memory system is his to keep improving; he inherits a working retrieval layer + a costed plan, not a blank sheet.

## Two premises corrected empirically (A-12 — why checking earned its keep)
- **"No retrieval layer" — wrong about the platform, right about the deployment.** The stack exists (`memory_search`/`memory_get`, promotion, REM dreaming, temporalDecay, idempotent flush); it was dormant (index 0/34, dreaming off).
- **The review's config keys don't exist as written; and its "single biggest lever" is false.** Real keys are under `agents.defaults.*`; `toolResultMaxChars` is **already 16 000** (read from the pinned dist) — a 1.5 M-token turn is the **un-reset long-lived `:telegram:direct` session + cache-read**, not one un-capped tool result. So the interim stabiliser is *retrieval* (done), **not** lowering that cap (which would risk truncating browser snapshots).

## Judgement calls (Peter: "trust your judgement")
- **Shipped** the reversible, no-restart, highest-value change (retrieval) and proved it.
- **Did NOT** apply the marginal config levers (`memoryFlush.model`, `cacheRetention`) — no urgent stabiliser survived the empirics, Peter has explicit config-thrash fatigue ([P6-108]), and each `openclaw.json` edit is D-class/A-7. They belong in a **measured** migration pass, not a late-night restart. openclaw.json untouched.
- **Deferred** dedup of the 9×-duplicated daily files (mutation risk on live memory; not required for the unlock; MMR handles recall noise) → Andreas backlog.

## SHA re-anchor (post-session)
- **openclaw.json** `da191cf1920d`/10099B — UNCHANGED, A-7 clean (live == last-good), 600/staff. Pin 2026.4.22 (00bd2cf), sandboxes `capable-2026-06`.
- **New:** `MEMORY-ARCHITECTURE-v2.md` `6d35e79f004f`/33514B.
- **Updated:** `PHASE-6-DEVIATIONS-LOG.md` `f369696f4107`/326101B (P6-118 + update); `PRE-COS-BACKLOG.md` `f00e3121c5fc`/22574B (§A.4 Andreas memory backlog).
- **Memory index (persisted, on disk):** pa 34/34·216 chunks, pro 31/31·36, `Dirty: no`. 7 dormant agents also indexed.
- **Deviations log:** P6-1…118 **gap-free, no dangling refs** (mechanical [P6-101] check run).

## Carry-chain audit ([P6-101])
- **Mechanical:** P6-1…118 gap-free; no referenced-but-undefined codes. Pre-existing 21/22 dupes untouched (additive-only).
- **Carry diff vs 6c.20 closure "carried → 6c.21":** ★ payments real-charge F26 = **CARRIED** (Stage-2 secondary all session; memory redesign was the lead by design); ③ reply-watcher = **CARRIED**; ④ context-lever cost-tuning pass = **DISPOSITIONED/superseded** (empirically toolResultMaxChars is already 16K → re-scoped into the Andreas measured-config-pass, A.4 #2 — not dropped); Battery re-run = **CARRIED**; Porsche re-deliver = **CARRIED** (now retrieval-assisted); generate-mode cred store = **CARRIED**; op-put username repair = **CLOSED** (Peter did it, per [P6-115] update). Nothing silently dropped.

## Carried → 6c.22 (in the opener)
- **Memory v2 migration — the measured config pass + file restructure** (the natural continuation; needs the sanctioned `openclaw.json` path Peter authorised in principle). #1 pre-Andreas blocker; retrieval now wants bedding-in + measurement.
- **Payments real-charge F26** (plain merchant, Marie gateway turn, Peter 2FA) — acceptance-gate blocker, still open ([P6-99]/[P6-117]).
- **Reply-watcher**, **generate-mode cred store validation**, **Porsche re-deliver** (Marie, deliver-first), **Battery re-run** (T3/T6/digest, cheapest model ~$5 cap).
- **Watch (empirical):** did turning retrieval on measurably reduce the [P6-116] pattern (rebuilds / "I don't know" / lost work) in real runtime?
