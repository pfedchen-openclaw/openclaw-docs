# SESSION 6c.22 — Memory v2 migration (measured config pass + file restructure) + retrieval-impact check

_Opener · generated at 6c.21 close (2026-07-05). 6c.21 **designed Memory Architecture v2** (`MEMORY-ARCHITECTURE-v2.md` `6d35e79f004f`/33514B — the build spec, supersedes the REVIEW doc), did the external best-practice research properly (cited, §2), and **shipped + verified migration Step 1: retrieval is ON** (index built pa 34/34·216, pro 31/31·36; `memory_search` proven returning the exact [P6-116] Porsche content at 0.667 that had returned `count:0`). Two review premises corrected empirically ([P6-118]): the retrieval stack was dormant, not missing; `toolResultMaxChars` is already 16 K (not the churn lever). openclaw.json **UNCHANGED** `da191cf1920d`/10099B (A-7 clean) — no config thrash by design. **Read `MEMORY-ARCHITECTURE-v2.md` first**, then [P6-118]. GUI/Screen-Sharing (D20) — the config pass + restart are GUI-bound._

## Stage 0 — Ledger (no mutating work until GREEN)
- **A-7:** live == last-good == **`da191cf1920d`/10099B**, 600/staff. Pin 2026.4.22 (00bd2cf). Sandboxes `capable-2026-06`. (openclaw.json is Read-denied + auto-mode-classifier-blocked — the config pass **needs the sanctioned `openclaw.json` path Peter authorised in principle**; confirm at open.)
- **Deviations currency:** **P6-1…118 gap-free**, no dangling refs (run the [P6-101] carry-chain check at close too). Pre-existing 21/22 dupes are historical — leave them.
- **Context check first ([P6-117]):** confirm GUI vs SSH empirically (`SSH_CONNECTION`, `launchctl managername`) before assuming — the config pass + restart are GUI-bound.
- **★ Retrieval-still-live check (the 6c.21 change):** `openclaw memory status` → `Indexed: 34/34` (pa) / `31/31` (pro), `Dirty: no`; a spot `memory_search` returns real hits. Index persists on disk (`~/.openclaw/memory/{pa,pro}.sqlite`) and re-syncs on session start — confirm it survived the overnight freshstart/restarts.
- **Build artefacts present?** `MEMORY-ARCHITECTURE-v2.md` `6d35e79f004f` · deviations `f369696f4107` · backlog `f00e3121c5fc` · MEMORY.md self-cap holding (`wc -c workspace-pa/MEMORY.md` < ~5900).

## Stage 1 — the LEAD: advance Memory v2 (follow `MEMORY-ARCHITECTURE-v2.md` §6)
The design is done and Step 1 is live. Continue the staged, **measured** migration — each lever measured before/after per [P6-106]/[P6-109] (calibrated rates; Admin API unavailable on Peter's individual account), **not batched blind**. In order:
1. **Empirical retrieval-impact check (free, inspection).** Read Marie/Charlotte's real runtime since 6c.21: did turning retrieval on reduce the [P6-116] pattern — fewer "I don't know"/rebuild-from-scratch, recall before re-deriving? This validates the biggest change. (Consider a single cheap-model smoke turn only if inspection is inconclusive — [P6-51].)
2. **Dedup the 9×-duplicated daily files + enable `mmr` + re-index** (backup-first; reversible). Removes recall noise (near-identical top hits) and stops the flush dup growing. No restart.
3. **The measured config pass (Peter-gated: sanctioned `openclaw.json` path + one GUI restart, A-7 staged/F17, P6-24 sync).** `compaction.memoryFlush.model`→cheap/local (stop per-interaction flush on Opus); `keepRecentTokens` (protect the active task through compaction); `truncateAfterCompaction` (bound the JSONL); `contextInjection:"continuation-skip"` (cache-stable prefix); weigh `params.cacheRetention` (note: 1 h TTL won't cover the 2 h heartbeat — marginal, measure). **Do NOT lower `toolResultMaxChars` (already 16 K).** Measure each.
4. **Address the un-reset long-lived `:telegram:direct` session** (the true source of the 355 K–1.5 M-token turns — excluded from the daily freshstart) via periodic **structured** compaction/checkpoint (not a blunt wipe — Peter values continuity, [P6-93]/[P6-95]).
5. **File restructure to the small frozen core** (≤~30 K: IDENTITY+SOUL+lean AGENTS + a `MEMORY.md` that indexes topic memories) + move procedural/episodic detail to retrieval-only (four-store split; procedural ≠ semantic). Live bind, no restart.
6. **Observability canary** (per-turn tokens, compaction/hour, truncation/day, daily-file dup count, index freshness, cache read:write ratio) → the acceptance gate (doc §8). Then **consolidation ON** (`dreaming` `0 3 * * *`, cheap model) only after dedup + reviewing the first `DREAMS.md`.

_All of the above is the Andreas-owned track (`PRE-COS-BACKLOG` §A.4) — advance as far as Peter's presence + the sanctioned config path allow this session; the design is the durable artefact._

## Stage 2 — carried (secondary — do not let these crowd out Stage 1)
- **Payments real-charge F26** (plain merchant, Marie gateway turn, Peter 2FA) — the other acceptance-gate blocker, [P6-99]/[P6-117]. Hard gateways stay drive-to-page + surface.
- **generate-mode credential store** — round out [P6-115] (only `existing` proven live via Amazon).
- **Reply-watcher** — host poller on the agents' live Gmail cred (skip gog, [P6-109]) → `openclaw agent … --deliver`.
- **Porsche warranty write-up** — Marie re-delivers cleanly (deliver-first; **now retrieval-assisted** — she can recall the prior context).
- **Battery re-run** (judgement class T3/T6/digest) via the real Telegram path; cheapest model, ~$5 cap ([P6-51]).

## Closed at 6c.21 (do not re-carry)
Memory v2 DESIGNED + Step-1 (retrieval) SHIPPED+verified [P6-118]; the review's toolResultMaxChars/no-retrieval premises corrected empirically; Andreas memory backlog wired (A.4). op-put username repair CLOSED (Peter, per [P6-115]); ZZ-TEST cred DELETED (Peter, [P6-117]).

## Safety rails
Reversible build runs without prompting (retrieval index build, dedup-with-backup, file restructure, canary scripts). **Surface irreversible/destructive:** openclaw.json changes + the **gateway restart** (disrupts live sessions — low-traffic, A-7 staged), version-pin/D1, real charges, 1Password writes/deletes (`op item edit`/`delete`/`vault`/`service-account` deny-listed — never route around), depairing, reboots. **Cost ([P6-51]):** inspection/host-drive first; cheapest model; no loops/Opus/batches for tests; **measure each context lever, don't apply blind** (6c.21 proved the value — the "biggest lever" wasn't). **Secrets ([F21/F51]):** op host-only; display-immune checks; never read openclaw.json/agent-creds/** into context. GUI/Aqua for restart + browser + 2FA (D20).

## Carry-forward
- **Acceptance gate** stays OPEN — reliability gate (doc §8) now has its foundation (retrieval live); payments mechanism-proven not F26-charged.
- **Pre-Andreas:** memory architecture is de-risked (designed + retrieval live) but the migration remainder + the acceptance gate are the #1 blocker (PRE-COS-BACKLOG §A.4). Then v7 capstone reconciliation ([P6-76], after the acceptance gate) → 6d Andreas.
- **Andreas mandate:** first-principles review/refactor of faulty foundations, not patching (Peter, extends P6-49) — PRE-COS-BACKLOG §C; the memory saga is the exemplar and Andreas inherits the full v2 plan.
