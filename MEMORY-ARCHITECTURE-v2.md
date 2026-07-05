# Memory / Context Architecture v2 — the foundational redesign

_Session 6c.21 (2026-07-05, GUI/Aqua). The LEAD deliverable. Author: Claude Code operator. Supersedes the direction in `MEMORY-CONTEXT-ARCHITECTURE-REVIEW.md` (6c.20) where empirical findings correct it — the review's diagnosis is right; two of its premises are wrong (below). Anchored on: the [P6-93/97/98] bootstrap-truncation root cause, [P6-102/103] flush artefacts, [P6-108]④/[P6-106] cost = context churn, [P6-116] the churn causing functional failure (lost Porsche deliverable, mis-report), and [P6-115] the daily-file duplication. openclaw.json at author time `da191cf1920d`/10099B (A-7 clean, 600/staff). Pin 2026.4.22 (00bd2cf)._

---

## 0. Headline — the reframe that changes the whole answer

The review asked us to **build a missing retrieval layer**. Empirically, **the platform already ships one — it is switched off on this deployment.** OpenClaw 2026.4.22 implements, natively, every one of the review's six "target" principles: hybrid embedding+FTS retrieval (`memory_search`/`memory_get`), the working/episodic/semantic split (session / `memory/YYYY-MM-DD.md` / `MEMORY.md`), promotion + REM "dreaming" consolidation, recency decay, and an idempotent pre-compaction flush. **None of it is running on Marie or Charlotte.**

Two facts, read live this session, prove it:

1. **The retrieval index has never been built.** `openclaw memory status`: `Indexed: 0/34 files · 0 chunks` (pa), `0/31` (pro); `Recall store: 0 entries · 0 promoted`; `Dreaming: off`. `memory_search` is *enabled* (`memorySearch.enabled=true`) but returns nothing, because nothing is indexed. So the agents run on **100% wholesale injection with zero retrieval** — exactly the "only knob is inject-more↔inject-less" the review diagnoses, but by *omission of configuration*, not by *absence of capability*.
2. **The review's config-key names are wrong for this build** (A-12). It cites `contextLimits.toolResultMaxChars`, `compaction.memoryFlush`, `params.cacheRetention`, `imageMaxDimensionPx` as top-level keys. In the pinned 2026.4.22 schema they are all nested under **`agents.defaults.*`** (e.g. `agents.defaults.contextLimits.toolResultMaxChars`) — which is why every prior `config get` on the assumed names returned "not found". §3 gives the real surface.

**Consequently v2 is mostly *activation + correct wiring + file restructuring of machinery we already own*, not net-new engineering.** That is a far stronger answer to "come up with something that finally works": it is proven code (OpenClaw's own memory-core), it is cheap, and it is reversible. The review's core insight stands — *retrieval over injection, three types handled correctly, compaction as a checkpoint* — we just reach it by turning the platform on rather than bolting something onto it.

> **Shipped this session (6c.21):** retrieval is **now live** — the index was built (pa 34/34·216 chunks, pro 31/31·36) and verified returning real hits on the exact [P6-116] query that had returned `count:0`. That is the single highest-value, reversible, no-restart step of the migration (§6 Step 1), done. The remaining steps are the measured config pass + file restructure + consolidation (§6), carried into the Andreas-owned improvement track (`PRE-COS-BACKLOG`).

---

## 1. Diagnosis — the real mechanism (corrected, from the build + live state)

Ten sessions of incidents are one failure with four coupled faults. Each is now evidenced from the live deployment, not inferred.

### Fault A — No retrieval in practice (the review's core point, confirmed by omission)
- `memory_search`/`memory_get` exist and are enabled, but the **index is empty** (`0/34`). The daily episodic files — **`2026-07-03.md` is 105 KB / 894 lines** — are written and never read back. They are, functionally, **write-only logs**.
- `MEMORY.md` is the *only* durable knowledge the agent actually sees, and it sees it as a **fixed blob injected every turn** (pa 5.7 KB, pro 6.2 KB), not as a recalled-by-relevance store.
- Net: the agent cannot recall selectively, so *everything durable must be injected*. That is the single-axis trap.

### Fault B — Compaction churn is the live functional failure ([P6-116])
- With `contextTokens=120000` and no tool-result cap, **turns reached 355 K–1.587 M tokens**; **3 compactions in ~45 min**, at least one **mid-task**. A 248 KB Porsche PDF and full browser DOM snapshots entered durable context and filled the window.
- The 12:57 compaction (tokensBefore 145 767) **ate Marie's rebuilt Porsche write-up before delivery** → Peter received nothing. The 13:35 compaction left her unable to see her own tool calls → she both **over-claimed forward** ("billing details filled") and **falsely self-accused backward** ("fabricated from thin air"). Both are compaction artefacts, not behaviour.
- Root: compaction is **size-triggered, lossy, uncontrolled, and can fire mid-task**. It is the single point of failure the review names.

### Fault C — The flush duplicates, and the loop compounds ([P6-115]/[P6-116])
- `memoryFlush.enabled=true` fires a silent "save important context" turn before each compaction. It **re-appends** a summary rather than updating a structured store. Measured: **`2026-07-03.md` has its section headings duplicated 9×** (e.g. "Completed today (with tool-verified receipts)" ×9), 07-04 ×3.
- This is the review's **positive-feedback loop**, empirically: big context → compaction → flush re-append → bigger daily file → (and the flush turn itself costs 6–12 K output tokens every interaction).

### Fault D — Cost is structural context churn ([P6-108]④/[P6-106]/[P6-109])
- Marie's heartbeat is **2 h**, past the **5-minute prompt-cache TTL**, so **every interaction re-cache-writes the whole context** (`cacheWrite` ~80–190 K tok/turn). Tool-heavy turns cache-*read* millions (one at 3.8 M). The un-capped tool results inflate both.
- Calibrated ([P6-109]): a ~395 K-token Opus turn ≈ $1.54; the test day ≈ $75 Opus. The cost *is* the context volume × turn count — precisely the review's "cost = context × turns".

### What is NOT the problem any more (so we don't refight it)
- **Raw bootstrap truncation is currently held.** [P6-98] read the build source: the *injected* set is 8 files (`AGENTS→SOUL→TOOLS→IDENTITY→USER→HEARTBEAT→(BOOTSTRAP)→MEMORY` ≈ 53 K chars for pa); `PA-PLAYBOOK`/`DECISIONS`/`USER-CONTEXT`/`TASKS` are **reference-only, never injected**. With `bootstrapTotalMaxChars=110000` the injected set fits, so truncation events are ~0 since [P6-98]. **But this is the review's "slide the slider" patch** (budget went 60 K→75 K→110 K) — pa's injected set will re-cross the cliff as files grow, and it does nothing for Faults A–D. v2 must *remove the dependence on the slider*, not widen it again.

---

## 2. External best-practice grounding

Two research tracks (Anthropic/vendor guidance; academic/industry architectures), primary-sourced this session. The convergent findings that shape v2:

### 2a. Anthropic / Claude-ecosystem guidance (cited)
- **Two-tier memory is the reference design.** Claude Code itself uses a small **always-loaded index** (`MEMORY.md`, first ~200 lines / 25 KB) that points to **topic files read on demand**; detail files are *not* loaded at startup. "Rather than loading all relevant information up front, an agent records what it learns in memory files and reads them back on demand" (memory-tool docs). This is v2's Principle 1, and OpenClaw already supports it.
- **Context is a depleting attention budget, not a bucket.** Recall degrades as the window grows ("context rot"; n² attention). Target "the smallest set of high-signal tokens." → justifies aggressive tool-result caps and a *small* injected core.
- **Cache economics are exact and decisive.** Cache **read = 0.1×** base input; **write = 1.25×** (5-min TTL) / **2×** (1-hour TTL). Invalidation is prefix-cumulative: *any* change at/before a breakpoint re-writes everything after. **Design rule: order the prompt stable→volatile, freeze the prefix (no `now()`/UUIDs), inject volatile content last.** Marie's 2 h idle > 5-min TTL is why she re-caches every turn; `cacheRetention:"long"` (1-hour TTL) and a frozen bootstrap prefix directly attack Fault D.
- **Three complementary boundary mechanisms, distinct roles.** *Context editing* clears stale tool results (Anthropic default trigger 100 K, keep last 3 → −84 % tokens on a 100-turn search). *Compaction* summarises (trigger 150 K). *Memory* preserves what summarisation would drop. Anthropic's explicit pattern: "compaction keeps the active context small; memory preserves the information that must survive summarization." And the discipline: **"ASSUME INTERRUPTION… you risk losing any progress not recorded in your memory directory"** + **"mark a feature complete only after end-to-end verification."** This is the checkpoint-before-compact fix for Fault B, and it validates [P6-116]'s coping rules as the *right* rules — just now backed by structure.
- **Tool-result hygiene at source.** Claude Code caps tool responses at **25 K tokens** by default; return high-signal fields not identifiers; paginate/filter/truncate; offer concise vs detailed. Large pages/PDFs/DOM must be summarised or offloaded to a file, never dumped whole.
- **Sub-agent isolation.** Give fan-out/enrichment work its own clean context and return a distilled summary; keeps the main window lean.

### 2b. Academic / industry memory architectures (cited)
Every serious system converges on the same taxonomy and the same fixes — and each maps to an OpenClaw feature we already have.
- **The four-store split (CoALA, arXiv 2309.02427; ACT-R/Soar lineage).** Working (live task) · episodic (past experience) · semantic (world/self facts) · procedural (operating rules/skills). This is the taxonomy the review's "three types" should be — Marie's *procedural* brief (`AGENTS.md`/`PA-PLAYBOOK`) is a fourth type we currently conflate with semantic. Procedural memory is "risky/costly to change" → evolve it via versioned, feedback-driven edits (matches our batch-apply convention [P6-10]), never silent self-rewrite.
- **MemGPT / Letta (arXiv 2310.08560) — the OS/paging model.** Context window = RAM, external store = disk, LLM = CPU. **Pin identity + user facts always-in-context** (the "persona"/"human" core blocks); **page everything else on demand** via search tools. Crucially, on memory pressure it runs a **save-then-evict handshake** (warn at ~70%, save to archival, *then* evict) — **never silent truncation**. This is exactly OpenClaw's `memory_search`/`memory_get` (disk) + injected core (RAM) — and exactly what our compaction must become (checkpoint, then compact).
- **Generative Agents (Park et al., arXiv 2304.03442) — scored recall + reflection.** Retrieval = weighted **recency (exp decay) + importance (LLM-assigned 1–10) + relevance (cosine)**, not similarity alone. Periodic **reflection** synthesises higher-level insights from recent records **with citations to the source events**, so the agent reasons from conclusions, not by re-reading logs. OpenClaw's `temporalDecay` = recency; hybrid BM25+vector = relevance; **dreaming** = reflection/promotion. Importance-scoring is the one piece not native — the promotion score-gates approximate it.
- **Idempotent reconciliation — the exact fix for our 9× duplication (Mem0, arXiv 2504.19413; LangMem).** Mature systems **never blind-append**: each new memory is reconciled against the top-k similar existing ones and the system chooses **ADD / UPDATE / DELETE / NOOP** (Mem0) or update/invalidate/consolidate (LangMem). Our `memoryFlush` re-appending is precisely the anti-pattern these were built to kill.
- **Supersession over deletion for changing facts (Zep/Graphiti, arXiv 2501.13956).** Facts that change (an address, a preference) get a **temporal validity window**; a contradiction *invalidates* the old fact rather than deleting it — "what did I believe on date X" stays answerable and stale facts stop out-ranking current ones.
- **Background formation + structured compaction target.** Form memory **in the background** ("sleep-time compute"), not on every hot-path turn (latency). And the compaction target should be a **structured running-state object** (open tasks, decisions, artefact pointers), updated incrementally — **not a freeform prose paragraph** (LangGraph `RunningSummary`; Cognition's dedicated compressor). This is why our freeform re-append flush both duplicates *and* loses structure.
- **Compact cheapest-first.** Evict stale tool results *before* summarising messages (near-lossless, ~64% token cut in Anthropic's example); when you must summarise, "maximise recall first, then precision."

_Caveat carried from the research: cross-vendor benchmark numbers are self-reported and mutually contested — v2 rests on the **mechanisms**, which are agreed, not on any single benchmark table._

### 2c. The synthesis
Every credible source points the same way and — critically — **OpenClaw already implements each mechanism**:

| Best-practice mechanism | OpenClaw feature (already present) | State on this deployment |
|---|---|---|
| Small always-loaded index + on-demand recall | injected `MEMORY.md` + `memory_search`/`memory_get` over daily files | index **empty**; MEMORY.md injected as blob |
| Retrieval scored by relevance **+ recency** | hybrid BM25+vector + `temporalDecay.halfLifeDays` | retrieval off; decay **off** |
| Reflection / consolidation of raw logs | `dreaming` (REM promote → MEMORY.md, `DREAMS.md` review) | **off** |
| Dedup / idempotent update vs append | `memoryFlush` (should update, not re-append) | **re-appends** (9× dup) |
| Checkpoint before the context boundary | deliver-before-anything + memory pointer | coping rule only (not structural) |
| Tool-result caps at source | `contextLimits.toolResultMaxChars`, media/pdf/snapshot caps | **unset** (defaults) |
| Cache-stable prefix, stable→volatile | `contextInjection:"continuation-skip"`, `cacheRetention` | defaults (`always`, short TTL) |

v2 is the act of switching this column from "off/unset" to "on/tuned", plus a file restructure so the injected core is genuinely small.

---

## 3. The real config surface (2026.4.22, secret-free via `openclaw config schema`/`get`)

Authoritative key names + current live values. This table replaces the review's assumed keys. "unset" = at schema default (not written to `openclaw.json`).

| Key (`agents.defaults.` unless noted) | Current | Default | Role / lever |
|---|---|---|---|
| `contextTokens` | **120000** | — | working-window cap; low → compaction fires often (Fault B) |
| `bootstrapMaxChars` | **30000** | 12000 | per-file injection cap |
| `bootstrapTotalMaxChars` | **110000** | 60000 | total injection cap — the "slider" (60→75→110 K history) |
| `contextLimits.toolResultMaxChars` | unset → **16000** | 16000 (`?? 16e3` in dist) | caps one live tool result. **Already tight** — read from the build this session; the review's "single biggest lever" premise is *false*, this is not the churn driver (a 1.5 M-token turn is not one 16 K tool result — it's cumulative session + cache-read) |
| `contextLimits.memoryGetMaxChars` | unset | (internal) | cap on `memory_get` reads |
| `contextLimits.postCompactionMaxChars` | unset | (internal) | AGENTS.md kept on post-compaction refresh |
| `imageMaxDimensionPx` | unset | 1200 | screenshot vision-token cap |
| `pdfMaxPages` / `pdfMaxBytesMb` | unset | 20 / 10 | PDF ingestion caps (the 248 KB Porsche PDF) |
| `browser.snapshotDefaults.mode` (top-level `browser.*`) | unset | — | DOM-snapshot extraction mode (browser bloat) |
| `tools.web.fetch.maxChars` / `maxResponseBytes` (top-level `tools.*`) | unset | — | web_fetch caps |
| `compaction.memoryFlush.enabled` | **true** | true | pre-compaction flush turn (re-appends → dup; costs 6–12 K out/turn) |
| `compaction.memoryFlush.model` | unset | (session model) | **offload flush to a cheap/local model** — Fault D |
| `compaction.keepRecentTokens` | unset | (internal) | protect the recent window through compaction — Fault B |
| `compaction.identifierPolicy` | unset→`strict` | strict | preserve opaque IDs/URLs in summaries (Wikimedia scramble) |
| `compaction.truncateAfterCompaction` | unset | — | rewrite JSONL to drop summarised entries (bounds the 12.5 MB transcript growth) |
| `memorySearch.enabled` | **true** | true | retrieval master switch (on, but index empty) |
| `memorySearch.sync.onSessionStart` / `onSearch` / `watch` | unset | true/true/true | **index build triggers** — defaults would build it; index is dirty/0 → provider never succeeded or was never invoked (verify at migration) |
| `memorySearch.query.maxResults` / `minScore` | unset | 6 / 0.35 | recall breadth / threshold |
| `memorySearch.query.hybrid.temporalDecay.enabled` / `halfLifeDays` | unset | false / 30 | **recency boost** (Generative-Agents-style) — off |
| `memorySearch.query.hybrid.mmr.enabled` | unset | false | de-duplicate near-identical recalls |
| `plugins.entries.memory-core.config.dreaming.enabled` / `frequency` | unset→false | false / `0 3 * * *` | **consolidation** (REM promote) — off |
| `contextInjection` (per docs) | unset→`always` | always | `continuation-skip` = don't re-inject bootstrap on continuation turns (cache-stability) |
| `params.cacheRetention` (per [P6-106]) | unset | — | `"long"` = 1-hour cache TTL (bursty 2 h-idle use) |

> **Note on the empty index — RESOLVED this session (retrieval is now live).** The empty index was **not** a broken provider: `openclaw memory status --deep` shows `Embeddings: ready · Vector: ready · FTS: ready` — the OpenAI embedding provider was wired and reachable all along. The index had simply **never been built** on this deployment (earlier "no probe output" was a `timeout`-not-installed artefact on macOS, not evidence). **`openclaw memory index --force` this session built it: pa 34/34 files · 216 chunks, pro 31/31 · 36 chunks, `Dirty: no`.** Verified working by inspection: the query "Porsche windscreen warranty write-up" (which returned `count:0` on the [P6-116] failure day) now returns the Porsche memory at **0.667 relevance** — the [P6-116] rebuild-from-scratch mechanism is killed at the retrieval layer. `sync.watch`/`onSessionStart` (both default true) keep it fresh; the sqlite store persists across restarts. _Follow-up: the pa daily files are 9×-duplicated, so recall is noisy (near-identical top hits) → dedup + enable `mmr` (backlog)._ **Local-embedder option retained** for privacy (no external key/spend) but not needed to unblock v2.

---

## 4. Memory Architecture v2 — the design

Six principles, each mapped to concrete OpenClaw wiring. The through-line: **a small, frozen, always-on core; everything else retrieved on demand; large payloads never persisted; the context boundary made safe; cost minimised by construction; regressions alarmed.**

### P1 — Retrieval over injection
- **Always-on core (injected every turn), kept small and frozen:** `IDENTITY` + `SOUL` (persona) + a **lean `AGENTS.md`** (hard limits, receipt rule, ladder — the genuinely hot rules) + a **`MEMORY.md` that is an *index*, not a blob** (≤ ~1.5 KB: pointers to topic memories + the 3–5 hottest standing rules). Target injected core **≤ ~25–30 KB** (well inside Anthropic's "small index" and far under `bootstrapTotalMaxChars`, so the slider is irrelevant).
- **Everything else retrieved:** procedural detail (`TOOLS.md` verb tables, `PA-PLAYBOOK.md`) and episodic daily files are reached via `memory_search`/`memory_get`, not injection. Turn the **index on** (Step 1) so this actually works.
- **Recency matters:** enable `temporalDecay` (`halfLifeDays` ~14–30) so "what happened lately" outranks stale notes; enable `mmr` so recall isn't 6 near-duplicate chunks.

### P2 — Separate the memory types, handle each correctly (the four-store split)
The research settles the taxonomy at **four** stores, not three — the missing one is *procedural*, which we currently conflate with semantic.
- **Working (the live task):** the session buffer — volatile, but **checkpointed** (P4). Deliverables never live only here.
- **Episodic (`memory/YYYY-MM-DD.md`):** append-only *capture*, but **read back by retrieval**, and **deduplicated**. These are indexed, never injected wholesale (they already aren't — but they're currently un-indexed, so they're lost). For facts that *change* (an address, a preference), prefer **supersession over deletion** (Zep-style validity) so "what did I believe then" stays answerable and stale facts stop out-ranking current ones.
- **Semantic (`MEMORY.md` index + promoted topic memories):** a **bounded, indexed, curated** KB. New durable facts are written as *topic memories* (indexed) with a one-line pointer in the `MEMORY.md` index — mirroring this operator's own memory system. **Dreaming** promotes qualified episodic signals into it nightly; humans review `DREAMS.md`. Self-cap on `MEMORY.md` size holds ([P6-115]).
- **Procedural (the brief — `AGENTS.md` hot rules + `PA-PLAYBOOK` detail):** operating rules/skills. High-leverage but **change-cautiously** — evolve via versioned, feedback-driven edits (our batch-apply convention [P6-10]), never silent self-rewrite. Hot rules stay in the always-on core; detailed procedure is retrieval-only.

### P3 — Tool-result hygiene at source (kills Fault B/D bloat)
- Set `contextLimits.toolResultMaxChars` (the biggest single lever) to cap any one tool result; `imageMaxDimensionPx` (keep 1200 or lower); `pdfMaxPages`/`pdfMaxBytesMb` sane; `browser.snapshotDefaults.mode` to the leanest faithful mode; `tools.web.fetch.maxChars`/`maxResponseBytes`. **A 248 KB PDF or full DOM must be extracted-then-discarded, never persisted.** Values are set empirically (§5 stabiliser: cap, then measure token drop before/after).

### P4 — Compaction as a safe checkpoint, not a guillotine (kills Fault B/C)
- **Deliver/persist before anything** — already the [P6-116] coping rule; now the *structural* contract: a turn writes its deliverable (sent/saved to a file) **and a `memory` pointer** before it can be compacted. Anthropic's "ASSUME INTERRUPTION" made real.
- **Save-then-evict handshake, not silent truncation** (MemGPT). The flush is a *warning to persist*, exactly like Anthropic's pre-eviction context-editing warning — the turn must save its deliverable + open-state *before* the boundary, and the compaction must not fire until it has.
- **Idempotent flush into a structured running-state, not freeform re-append** (fixes the 9× duplication). The flush target should be a **structured "open-state" block** (open tasks · decisions · artefact pointers) that is **rewritten (UPDATE), not appended** — the Mem0 ADD/UPDATE/DELETE/NOOP discipline, and the LangGraph structured-`RunningSummary` pattern, rather than a fresh prose summary each time. If `memoryFlush` cannot be made idempotent by config, redirect its target to this block. Offload the flush turn to `memoryFlush.model` = a **cheap/local model** (Fault D — stop paying Opus for housekeeping).
- **Compact cheapest-first** — evict stale tool results before summarising messages (near-lossless), and when summarising "maximise recall first, then precision."
- **Protect the active task** — `keepRecentTokens` high enough that an in-flight task survives a compaction; `identifierPolicy=strict` (kept) so IDs/URLs aren't scrambled; `truncateAfterCompaction` to bound transcript growth (the 12.5 MB JSONL).

### P5 — Cost is structural (context × turns), minimised by construction (kills Fault D)
- **Cache-stable prefix:** freeze the injected core order, no volatile tokens (timestamps/UUIDs) in the prefix; `contextInjection:"continuation-skip"` so continuation turns don't re-inject and bust the cache. `params.cacheRetention:"long"` for the bursty 2 h-idle pattern (1-hour TTL → far fewer re-writes).
- **Fewer/cheaper flush turns** (P4), **smaller injection** (P1), **capped tool results** (P3), **right-sized `contextTokens`** — re-tune upward *only after* the bloat is capped (raising the cap on capped context reduces compaction frequency without re-inflating cost).

### P6 — Observability (a regression trips an alarm, not Peter)
Extend the existing `cost-notify` + watchdog canaries with a **memory-health canary**: per-turn tokens (median + p95), **compaction frequency/hour**, **truncation-events/day** (already tracked), **daily-file size + duplicate-heading count**, **index freshness** (`indexed==total`, not `0/34`), and **cache read:write ratio** (a persistent low ratio = a prefix-buster). Emit a daily one-liner; alarm on threshold breach.

---

## 5. Validation against the failure history (v2 must kill each incident)

| Incident | Root cause | v2 mechanism that prevents it |
|---|---|---|
| [P6-93/97] MEMORY.md / TOOLS.md truncation ("she forgets") | wholesale injection > budget | P1: small frozen core; detail retrieved not injected → budget no longer binds |
| [P6-116] Porsche deliverable lost mid-compaction | no checkpoint; compaction mid-task | P4: deliver-before-compact contract + `keepRecentTokens` protects the task |
| [P6-116] "billing filled" over-claim + backward self-accusation | post-compaction blindness to own tool calls | P4: `identifierPolicy`/receipt persisted pre-compact; retrieval re-grounds state; coping rules retained |
| [P6-115] daily file duplicated 9× | flush re-appends | P4: idempotent flush (update, not append) + `mmr` on recall |
| [P6-108]④/[P6-106] cost = context churn | 2 h idle > cache TTL; un-capped tool bloat | P3 caps + P5 cache-stable prefix + `cacheRetention:long` + cheap flush model |
| [P6-102/103] flush-turn over/under-claim | flush turn on restricted toolset | P4: cheap-model flush + the mirror rule; flush no longer a reasoning turn on Opus |
| [P6-98] overnight breakdown (booted without full brief) | budget-exhausted bootstrap | P1: core always fits; behaviour rules in the core, procedure retrieved |
| Recurring "relearns the same lesson" | semantic store injected-as-blob, truncatable | P2: durable rules = indexed topic memories, recalled by relevance, promoted by dreaming |

A design that could not draw this table would not be done. Every past incident maps to a specific v2 mechanism.

---

## 6. Migration plan (staged, F17 / P6-24, measured, reversible)

Ordered least-risk → most-structural; each step measured before/after per [P6-106]/[P6-109]. **All openclaw.json edits are Peter-gated** (the auto-mode classifier blocks even reading it; a sanctioned path is needed — surfaced §9). Config edits: staged `openclaw.json.new` → atomic `mv` → `chmod 600 && chgrp staff`, `config validate`, last-good sync after verify, SHA-anchor before/after. Per-agent `pa`/`pro` sandbox blocks synced (P6-24).

1. **Turn retrieval on + prove it (no restart, reversible) — ✅ DONE 6c.21.** Provider confirmed ready (`--deep`); `openclaw memory index --force` built pa 34/34·216 chunks, pro 31/31·36 chunks (`Dirty: no`); `memory_search` verified returning real hits (Porsche query 0.667). Agents have recall for the first time. *(Cheap embedding spend only, not billed agent turns.)* **Remaining under this step:** dedup the 9×-duplicated daily files + enable `mmr` so recall isn't near-identical hits; then re-index.
2. **Tool-result hygiene caps (config + restart) — the interim stabiliser (§7).** Set `contextLimits.toolResultMaxChars`, `imageMaxDimensionPx`, `pdf*`, `browser.snapshotDefaults.mode`, `tools.web.fetch.*`. Measure per-turn token drop. Biggest immediate win on Faults B/D.
3. **Flush + compaction hygiene (config + restart).** `memoryFlush.model` → cheap/local; confirm/repair flush de-duplication; set `keepRecentTokens`, `truncateAfterCompaction`; keep `identifierPolicy=strict`. Measure compaction frequency + duplication.
4. **Cache stability (config + restart).** `contextInjection:"continuation-skip"`, `params.cacheRetention:"long"`; freeze bootstrap order; audit the prefix for volatile tokens. Measure cache read:write ratio.
5. **File restructure to the small frozen core (workspace edits, live bind, no restart).** Slim `AGENTS.md`/`MEMORY.md` to core+index; move procedural detail to retrieval targets; convert standing rules to indexed topic memories with index pointers. Re-measure injected-core size (target ≤ ~30 KB).
6. **Consolidation on (cron).** Enable `dreaming` (`0 3 * * *`), a cheap model; review first `DREAMS.md` output before trusting promotion. De-duplicate the existing bloated daily files as a one-off.
7. **Observability canaries (scripts + LaunchAgent).** Ship the P6 memory-health canary; wire into the daily digest / watchdog.

Re-tune `contextTokens` upward (Step 8, optional) only after Steps 2–5 have shrunk per-turn context — a higher cap on *capped* context lowers compaction frequency without re-inflating cost.

---

## 7. Interim stabiliser — revised by the 6c.21 empirics

The review proposed *lowering `toolResultMaxChars`* as "the single biggest lever." **This session's build-read killed that premise: the cap is already 16 000 chars** — a 1.5 M-token turn is cumulative session + cache-read, not one un-capped tool result. Lowering it further has little upside and would risk truncating browser snapshots for the payment work. So the real stabiliser is not one config flag; it is:

1. **Retrieval ON (Step 1) — ✅ done this session, no restart, reversible.** This is the actual highest-leverage move: the agents can now recall instead of re-injecting/rebuilding, which is the direct fix for the [P6-116] functional failures. It is the stabiliser.
2. **Then, as a *measured* config pass (Peter-gated, one GUI restart, A-7 staged):** `compaction.memoryFlush.model` → a cheap model (stop running the per-interaction flush on Opus — [P6-108]④); weigh `params.cacheRetention` (note: 1 h TTL still won't cover the 2 h heartbeat gap — marginal, measure before trusting); and address the **un-reset long-lived `:telegram:direct` session** (the true source of the 1.5 M-token cumulative turns — it is excluded from the daily freshstart) via periodic structured compaction. Measure each per [P6-106]/[P6-109]; do **not** batch blind.

The deliberate call this session was to ship the reversible, no-restart, high-value change (retrieval) and **not** thrash `openclaw.json` for marginal levers — config changes belong in a measured migration pass, not late-night ([P6-108]).

---

## 8. Acceptance gate (measurable — v2 is "done" when these hold for a week)

Reliability gate (prerequisite to, and now blocker of, the [P6-76] usefulness gate):

- **Zero truncation events / week** (bootstrap-truncation canary flat).
- **Zero lost deliverables** — no compaction-eaten in-flight work; every "done" has a persisted artefact pointer.
- **Median per-turn tokens < target** (set the target from the Step-2 before/after; interim proposal: median < ~60 K, p95 < ~200 K — no more 1.5 M turns).
- **Compaction frequency < N/hour** during active work (proposal: < 1/hour median; never > 2 mid-task in a session).
- **Calibrated daily cost < target** (Console-authoritative; proposal: steady-state well under the [P6-108] ~$75 test-day; set from Step-2/4 measurement).
- **Daily-file duplication = 0** (duplicate-heading count 1 per heading) and **index freshness green** (`indexed==total`, not `0/34`).
- **Retrieval actually used** — `memory_search` hit-rate > 0 on real tasks (proves the agent recalls rather than relying on injection).

Each threshold is finalised from the Step-2 baseline measurement; the gate is a canary that alarms on breach, not a one-off check.

---

## 9. Decisions / unblocks needed from Peter

1. **Sanctioned `openclaw.json` read/edit path for this work.** The auto-mode classifier blocks reading it; the config steps (2–4) need staged F17 edits + validation. Options: a one-session exemption, or Peter applies the staged `.new` files himself. **This is the gating unblock.**
2. **Gateway restart windows (D20, GUI).** Steps 2–4 each need a restart (disrupts live sessions). Batch them or space them to measure each lever cleanly.
3. **Cheap flush/dreaming model choice.** A small local model (e.g. an Ollama model) vs Sonnet for `memoryFlush.model` / `dreaming.model`. Local = ~free but needs the runtime; Sonnet = cheap-enough and already available.
4. **Embedding spend + provider.** Retrieval uses OpenAI `text-embedding-3-small` (cents to index; ongoing per-search cost is tiny). Confirm the OpenAI embedding key is live, or switch to a local GGUF embedder (no external spend, no external dependency for personal memory).
5. **Measurement authorisation.** Before/after each lever should be read from real runtime; scoring is by inspection (zero marginal cost), but confirming end-to-end needs a small number of real turns (cheapest model, [P6-51]).

---

## 10. What this is not
The [P6-116] coping rules (verify "ready/filled" like "done"; deliver-before-anything; reconstruct-don't-fabricate) are **kept** — v2 makes them structural rather than aspirational, but they remain correct and cheap. And v2 does not touch the *other* proven-flawed substrate — payment-form automation on hard gateways ([P6-111]/[P6-117]) — which is a separate track. This document is the memory/context redesign only.
