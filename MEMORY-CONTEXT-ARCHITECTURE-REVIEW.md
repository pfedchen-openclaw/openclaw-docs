# Memory / Context Architecture — Fundamental Review & Redesign Brief

> **⚠ SUPERSEDED by `MEMORY-ARCHITECTURE-v2.md` (6c.21, [P6-118]).** This review's *diagnosis* stands (retrieval-over-injection, the feedback loop, the memory-type conflation), but 6c.21 corrected **two of its premises empirically:** (1) the retrieval layer is **not missing — OpenClaw ships it; it was switched off** (index 0/34) and is now turned ON; (2) its config-key names are wrong (real keys are under `agents.defaults.*`) and its "single biggest lever" `toolResultMaxChars` is **already 16 K** (not the churn driver). Read `MEMORY-ARCHITECTURE-v2.md` as the build spec; this doc is kept for provenance only.

_Opened 2026-07-04 (6c.20→21) at Peter's direction: "we keep hitting memory / context / bloating / compaction / cost over the last ~10 sessions — it's at the crux of all failures AND high cost. Stop patching a fundamentally flawed system; review core principles + best practice (our KB + latest credible sources), refactor the build, come up with something that finally works." This doc is the **foundation for a dedicated session**; it is analysis + a design direction, not yet a committed build._

## 1. The pattern — one failure wearing many masks
Ten sessions of "different" incidents are the **same** failure:
- [P6-85] contextTokens 200K→120K + cost hardening · [P6-93]/[P6-97] MEMORY.md truncation → root-caused to the **bootstrap injection budget** · [P6-98] Charlotte overnight breakdown · [P6-102] Marie "fabricated" completed actions + silent grind · [P6-106] Marie kept on Opus, context levers *identified but not applied* · [P6-108] cost measured = **context churn** (~100k cache-*write*/turn, multi-million cache-*read*, a memory-flush turn per interaction) · [P6-109] cost calibration · [P6-115] MEMORY.md self-cap · [P6-116, today] the churn is proven to cause **functional** failure: turns at **355k–1.5M tokens**, **3 compactions in 45 min** (one mid-task) ate the Porsche deliverable and scrambled Marie's account of the Wikimedia flow.

Truncation, duplication, lost work, mis-reporting, and cost are **not five problems**. They are five readouts of one thing.

## 2. The core flaw (first principles)
**The architecture has no retrieval layer. Every piece of durable knowledge is either injected wholesale on every turn (the bootstrap files) or written to flat, append-only logs that are never selectively read back. There is no "store a lot, recall a little, on demand."**

Consequently the *only* control knob is a single axis:
- **inject more** → bloat → cost, frequent compaction, lost work; or
- **inject less** → truncation → forgetting.

Every patch we've shipped just slides the slider along this one axis (raise the cap, slim MEMORY.md, self-cap, cheaper model, prune). None *adds a dimension*. That's why something always pops back up: **we are optimising a system that is missing a component, not misconfigured.**

### 2a. The feedback loop that makes it worse over time
```
big context ─▶ compaction fires ─▶ memory-flush APPENDS a fresh summary
   ▲                                            │
   └────── larger bootstrap injection ◀── daily/curated memory grows (+duplicates)
```
More context causes compaction; compaction appends a re-summary; the memory files grow (today: the daily file was **3× duplicated** by re-append); bigger files mean bigger bootstrap injection; which means more context. It is **positive feedback** — it degrades with age, which is exactly the "last 10 sessions, getting worse" signature.

### 2b. Three kinds of memory, all handled the same wrong way
| Type | What it should be | What it is now | Failure it causes |
|---|---|---|---|
| **Working** (the live task) | volatile, but **checkpointed** so deliverables survive | lives in the same buffer compaction guillotines | lost Porsche write-up; mid-task amnesia |
| **Episodic** (what happened — daily logs) | append-only but **retrieved on demand**, deduplicated | re-injected wholesale + re-appended/duplicated | bloat, cost, the feedback loop |
| **Semantic** (learned rules — MEMORY.md) | a curated, **bounded, indexed** KB, recalled by relevance | a **fixed-size injected blob** with a truncation cliff | silent rule-loss; the recurring truncation |

Conflating all three into "write a flat file, then inject the whole file" is the root design error.

## 3. Why compaction is the single point of failure
Compaction today is **size-triggered, lossy, and uncontrolled**. It can fire mid-task and destroy in-flight work; it re-summarises (information loss); and it is the thing that writes the duplicative flush. A correct design treats a context checkpoint as a **safe, idempotent boundary** (deliverables already persisted; flush *updates* a structured store rather than appends a re-summary; the current task is protected), not as a guillotine that happens to also take notes.

## 4. Redesign principles (the target)
1. **Retrieval over injection.** A minimal always-on core (identity + the few genuinely hot rules) and **everything else recalled on demand, indexed by relevance.** _(This is precisely the model Claude Code itself uses — a small `MEMORY.md` index + on-demand file recall. It is proven and already conceptually present in the toolset; Marie just doesn't use memory this way.)_
2. **Separate the three memory types and handle each correctly** — checkpoint working; dedup + retrieve episodic; bound + index semantic.
3. **Tool-result hygiene at source.** Cap `toolResultMaxChars`; extract-then-discard large payloads; a 248 KB PDF or a full DOM snapshot must **never persist** in durable context.
4. **Compaction as a safe checkpoint, not a guillotine** — persist deliverables *before* it can fire; make the flush **idempotent** (update, not append-duplicate); protect the active task.
5. **Cost is structural: context × turns.** Minimise both by construction — smaller injection, fewer flush-turns, a right-sized model/context budget, and **cache-stable prefixes** (the bootstrap prefix should change rarely, so prompt-cache reads stay cheap).
6. **Observability.** Per-turn token + compaction-rate telemetry + a memory-size canary, so a regression trips an alarm instead of Peter noticing a failure.

## 5. Dedicated-session plan
**Inputs to review**
- *Our KB:* the P6-code chain in §1; **the full openclaw.json memory/context config surface** — `contextLimits.toolResultMaxChars`, `compaction.*` (incl. `memoryFlush`), `params.cacheRetention`, `contextTokens`, `imageMaxDimensionPx` — enumerate what each does and its current value (needs a sanctioned read of openclaw.json).
- *Latest credible sources:* OpenClaw's own memory-management documentation for the 2026.4.22 build; and established agent-memory-architecture practice — retrieval-augmented / external memory, the working-vs-episodic-vs-semantic split, context-window management for long-running agents, and Anthropic prompt-caching economics (cache-write vs cache-read cost, TTL). _(Deliberately deferred to the session so it's done properly, not hand-waved here.)_

**Method**
1. Clean-slate design of Memory Architecture **v2** against the six principles.
2. **Validate against the failure history** — v2 must demonstrably kill *each* past incident in §1 (a design that doesn't explain how it prevents the Porsche loss / the truncation / the cost isn't done).
3. Migration plan (agents, briefs, config, memory files) under F17/P6-24 discipline.
4. **Acceptance gate** — measurable: e.g. a week with zero truncation events and zero lost deliverables; median per-turn tokens under a target; calibrated daily cost under a target; compaction frequency under N/hour.

**Deliverables:** the v2 design doc + migration steps + the acceptance gate + the interim stabiliser (below) applied and measured.

## 6. Interim stabiliser (before the full redesign — stops the bleeding, pre-empts nothing)
Apply the **`contextLimits.toolResultMaxChars`** cap (the single biggest lever — it directly kills the browser-snapshot/PDF-dump bloat that drove today's 1.5M-token turns), and confirm/repair the `memoryFlush` **de-duplication**. Measure before/after per [P6-106]. This needs an `openclaw.json` F17 edit + a **gateway restart (GUI-bound, D20)**, and the auto-mode classifier currently blocks me from even reading `openclaw.json` — so it is **Peter-gated**, done in a Screen-Sharing session. It is compatible with, and independent of, the v2 redesign.

## 7. Honest note
Today's PA-PLAYBOOK additions ([P6-116]: verify "ready/filled" like "done"; deliver-before-anything; reconstruct-don't-fabricate) are **coping rules for a broken substrate**. They reduce harm; they are not the fix. The fix is §4–§5. Keep the coping rules (they're cheap and correct), but do not mistake them for having solved anything.
