# SESSION 6c.21 — Memory / Context Architecture v2 (the foundational redesign)
_Opener · generated at 6c.20 close (2026-07-04). **Peter's directive:** ~10 sessions of memory/context/bloat/compaction/cost failures are one flaw, not many — stop patching, review core principles + best practice (our KB + latest credible sources), refactor, "come up with something that finally works." This is the LEAD and the whole point of the session. Read **`MEMORY-CONTEXT-ARCHITECTURE-REVIEW.md`** FIRST (it is the brief), then [P6-116] (diagnosis) + [P6-108]/[P6-106] (cost evidence) + [P6-93]/[P6-97]/[P6-98]/[P6-115] (the truncation history). Everything else this session is explicitly secondary — do not let it crowd out the redesign. openclaw.json UNCHANGED `da191cf1920d`/10099B (A-7 clean); the context levers are applied DURING this session (GUI restart). **GUI/Screen-Sharing (D20)** — the interim stabiliser + any config change need the restart._

## Stage 0 — Ledger (no mutating work until GREEN)
- **A-7:** live == last-good == **`da191cf1920d`/10099B**, 600/staff. Pin 2026.4.22. Sandboxes `capable-2026-06`. (openclaw.json is Read-denied AND the auto-mode classifier blocks reading it via other tools — a **sanctioned read/edit path is needed** for the config work; surface to Peter at session open.)
- **Deviations currency:** **P6-1…117 gap-free**, no dangling refs (run the [P6-101] carry-chain check at close too). Pre-existing 21/22 dupes are historical — leave them.
- **Context check first (Peter, [P6-117]):** confirm GUI vs SSH empirically (`SSH_CONNECTION`, `launchctl managername`) before assuming — this session needs GUI-bound ops.
- **Build artefacts present?** `scripts/cred-save.sh` `389c46e89c10` · `cred-save-drain.sh` `4e714117e36c` · watcher `ai.openclaw.cred-save-watch` (live, gui/502) · PA-PLAYBOOK `23a571ff1817` · MEMORY.md `e2e028cf3c64`/5707B (self-cap holding? `wc -c` < 5909).

## Stage 1 — the redesign (the session)
Follow `MEMORY-CONTEXT-ARCHITECTURE-REVIEW.md` §5 (method) and §6 (interim stabiliser). In order:
1. **Interim stabiliser FIRST — stop the bleeding, measure.** Apply `contextLimits.toolResultMaxChars` (the single biggest lever — caps the browser-snapshot/PDF-dump bloat behind today's 1.5M-token turns) via a staged `openclaw.json.new` (F17) + GUI gateway restart; confirm `memoryFlush` de-duplication (today's daily memory was 3×-duplicated). **Measure before/after** per [P6-106] (calibrated Console rates, [P6-109]). This is Peter-gated (needs the sanctioned openclaw.json path).
2. **Review inputs.** Our KB — the [P6-116] §1 failure chain + the **full openclaw.json memory/context config surface** (`contextLimits.*`, `compaction.*` incl. `memoryFlush`, `params.cacheRetention`, `contextTokens`, `imageMaxDimensionPx`). Latest credible sources — OpenClaw's own 2026.4.22 memory docs + established agent-memory architecture (retrieval-augmented / external memory; working vs episodic vs semantic; long-running-agent context management; Anthropic prompt-caching economics).
3. **Design Memory Architecture v2** against the 6 principles (retrieval-over-injection; separate the three memory types; tool-result hygiene at source; compaction-as-safe-checkpoint; cache-stable prefixes; observability). **Validate against the failure history** — v2 must demonstrably kill each incident in [P6-116] §1 (truncation, lost Porsche deliverable, duplication, mis-report, cost). A design that can't explain how it prevents each isn't done.
4. **Migration plan** (agents, briefs, config, memory files) under F17 / P6-24. **Acceptance gate** — measurable: a week with zero truncation events + zero lost deliverables; median per-turn tokens under target; calibrated daily cost under target; compaction frequency under N/hour + a memory-size canary.
5. **Deliverables:** the v2 design doc + migration steps + acceptance gate + the stabiliser applied & measured.

## Stage 2 — carried (secondary — do NOT let these crowd out Stage 1)
- **Payments F26** (plain merchant, real charge, Peter 2FA) — acceptance-gate blocker, [P6-99]. Still open; the [P6-117] browser-automation reality means hard gateways stay drive-to-page + surface.
- **generate-mode credential store** — round out the [P6-115] mechanism (only `existing` proven live via Amazon).
- **Reply-watcher** — host poller on the agents' live Gmail cred (skip gog, [P6-109]) → `openclaw agent … --deliver`.
- **Porsche warranty write-up** — Marie to re-deliver cleanly (deliver-first), if not superseded.
- **Battery re-run** (judgement class T3/T6/digest) via the real Telegram path; cheapest model, ~$5 cap ([P6-51]).
- **op-put username data-repair** — Opera/Body4Life items pre-[P6-110] hold `openclaw` not the email; `op item edit` deny-listed → Peter's call.

## Closed at 6c.20 (do not re-carry)
Credential take-over mechanism SHIPPED + F26-proven (Amazon) [P6-115]; Gmail outage fixed [P6-113]; Wikimedia £20 DONE + ZZ-TEST cred DELETED (Peter, manual) [P6-117]; MEMORY self-cap [P6-115]; PA-PLAYBOOK coping rules [P6-116].

## Safety rails
Reversible build runs without prompting. **Surface irreversible/destructive:** openclaw.json changes + the **gateway restart** (disrupts live sessions — do at a low-traffic moment, A-7 staged), version-pin/D1, the real charge, 1Password writes/deletes (`op item delete`/`edit`/`vault`/`service-account` deny-listed — never route around), depairing, reboots. **Cost ([P6-51]):** inspection/host-drive first; cheapest model; no loops/Opus/batches for tests; measure each context lever, don't apply blind. **Secrets ([F21/F51]):** op host-only; display-immune checks; never read openclaw.json/agent-creds/** into context. GUI/Aqua for restart + browser + 2FA (D20).

## Carry-forward
- **Acceptance gate** stays OPEN — payments mechanism-proven not F26-charged; and now **reliability is gated on the memory redesign** (this session).
- **Pre-Andreas:** memory architecture is the **#1 blocker** (PRE-COS-BACKLOG A.4 / Top-9 #1). Then v7 capstone reconciliation ([P6-76], after the acceptance gate) → 6d Andreas. Full outstanding-before-Andreas list + session estimate: see the 6c.20 close assessment + PRE-COS-BACKLOG.
- **Andreas mandate captured:** first-principles review/refactor of faulty foundations, not patching (Peter 4-Jul, extends P6-49) — PRE-COS-BACKLOG §C.
