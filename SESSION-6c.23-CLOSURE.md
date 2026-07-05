# SESSION 6c.23 — CLOSURE: Memory v2 measured — the levers' before/after (tail killed, residue mapped), freshstart already-live correction, conservative core restructure, and the media-caps pass applied

_Closed 2026-07-05 (GUI/Aqua, D20 satisfied). Continuation of the 6c.21/6c.22 memory redesign: retrieval live (6c.21), dedup+canary+7 levers applied (6c.22); this session **measured** the applied levers, **corrected** the item-4 premise, **shipped** the conservative frozen-core restructure, and **applied** the media-caps config pass. New code: **P6-120**. openclaw.json CHANGED (media caps): `d702ae330bb7`/10509B → `eb08cfa7d7b6`/10654B (A-7 clean)._

## One-line result
The config levers + the daily freshstart clear **killed the catastrophic tail** (p95 3.76M→289K, max 16.4M→289K, 51→0 compactions) — but **confounded**, and the byte-cap truncation (pa 20%), the fat median (229K ≈ 3.8× target) and **zero organic recall** persist, so the reliability gate stays **OPEN**; the two remaining levers (frozen-core restructure, media caps) both advanced this session — restructure shipped conservative (−1.3K, zero risk), media caps applied + restarted + free-verified.

## What landed (P6-120)
1. **Canary before/after — DONE, honest, confounded.** pa 229K med / 289K p95 / 20% trunc / 0 compact; pro 145K / 179K / 0% / 0; both recall 0, retention short. The multi-million-token turns + 51-compaction days are gone, but that's post-freshstart-clear on a quiet day — not cleanly the levers. **True-positive residue → items 3/5:** 256 KB byte-cap truncation (pa 20%), median ≈ 3.8× the 60K target, recall 0. §8 thresholds **kept** (breaches are real). `retention:"short"` unfixable via config (API-tier) — note for Andreas.
2. **Item-4 CORRECTED — freshstart is already LIVE, not disabled.** The plist is loaded + enabled + 06:55-scheduled; the log shows it clearing **pa:main 2.73M (07-04)**/76.8K (07-05) + pro daily since ~07-02. So "enable it" was already done. **Real gap:** pa:main regrew **0→1.43M by mid-afternoon 07-05 from one heavy browser turn** — the daily reset doesn't bound *intra-day* bloat. Fix = media caps (below) + watchdog net + a future threshold-triggered reset. Current state healthy (pa:main absent, pro:main 37K).
3. **Conservative restructure — SHIPPED, zero capability risk (Peter's choice; no restart).** Injected core measured ≈ **50K** (PA-PLAYBOOK/DECISIONS confirmed NOT injected). Moved TOOLS.md browser rationale/technique/toolchain description (all already in PA-PLAYBOOK) → new **indexed** `memory/reference-tools.md`; compressed AGENTS §Talking repetition. **Kept every hot rule + every verb + capability facts + L1 gate injected** (grep-verified). Core **49,997→48,675B (−1.3K)**; reference indexed (35 files/125 chunks) + retrievable (0.51–0.64). Backup kept. The ≤30K target needs the aggressive slice (deferred — Andreas + a billed verify turn).
4. **Media-caps pass — APPLIED (Peter's explicit "Stage + apply now").** `scripts/apply-6c23-media-caps.sh` (F17, self-verifying, rollback). **`browser.snapshotDefaults.mode="efficient"`** (primary — the browser-DOM source of the 1.43M bloat), `imageMaxDimensionPx 1200→1024`, `pdfMaxPages 20→15`, `pdfMaxBytesMb 10→8`; **toolResultMaxChars untouched** (16K); web.fetch held (no default). P6-24 clean (9 agents). Restart clean (pid 66862); index survived. **Free F26:** `mode:efficient` snapshot of example.com returns a valid ref-bearing lean tree — doesn't break the browser path. Caps' before/after accrues via canary as browser turns run.

## A-12 corrections that earned their keep
- `browser.snapshotDefaults.mode` = `z.literal("efficient")` — **binary, not an enum**; de-risked the top lever (no bad-value that could break bookings).
- `imageMaxDimensionPx`/`pdfMaxPages`/`pdfMaxBytesMb` live **directly under `agents.defaults`**, not `contextLimits`.
- opener's `temporalDecay` path was abbreviated — real key is `…memorySearch.query.hybrid.temporalDecay.enabled`.
- No CLI reports the bootstrap manifest — derived the injected set from the dist.

## Judgement calls
- **Surfaced the restructure scope + the billed turn rather than editing Marie's LIVE brief unilaterally** — with recall=0, moving hot content to retrieval-only risks capability loss until a turn proves she searches; that's a trust/scope choice (and Andreas-owned). Did the zero-risk conservative slice Peter chose; free-verified it (size + retrievability + grep), no billed spend.
- **Applied media caps under Peter's explicit permission** (like 6c.22) — never weakened the F21 backstop; structure-driven jq, display-immune checks, no secret dumped.
- **Kept the §8 thresholds unchanged** — the breaches are true positives flagging items 3/5, not miscalibration.
- **Held web.fetch caps** — no confirmed default + not implicated; don't apply blind (A-12 / opener discipline).

## SHA re-anchor (post-session)
- **openclaw.json** `eb08cfa7d7b6`/10654B — CHANGED (media caps), A-7 clean (live==last-good), 600/staff. Pin 2026.4.22 (00bd2cf), sandboxes `capable-2026-06`.
- **Updated:** `PHASE-6-DEVIATIONS-LOG.md` `77203844bb31`/350306B (P6-120).
- **Restructured (workspace-pa):** `AGENTS.md` `f73e3d62b7a6`/16670B (was 17406), `TOOLS.md` `eef039c7a89e`/15939B (was 16525), `MEMORY.md` `e2e028cf3c64`/5707B (unchanged); NEW `memory/reference-tools.md` `4eb0cd239c5d`/2871B (indexed, 0600).
- **New:** `scripts/apply-6c23-media-caps.sh`; `_backups-6c23-restructure/` (pre-edit AGENTS/TOOLS/MEMORY); `openclaw.json.prev-6c23-mediacaps`.
- **Memory index (persisted):** pa 35/35·125 chunks, pro 31/31·36, `Dirty: no`.
- **Deviations log:** P6-1…120 **gap-free, no dangling refs**.

## Carry-chain audit ([P6-101])
- **Mechanical:** P6-1…120 gap-free; no referenced-but-undefined codes. Pre-existing 21/22 dupes untouched.
- **Carry diff vs 6c.22 closure "Carried → 6c.23":** ① canary post-config-pass = **DONE**; ② fresh-session retrieval re-test = **CARRIED** (billed turn, deferred/surfaced — Andreas+cost); ③ file restructure = **DONE (conservative)**, aggressive **CARRIED**; ④ long-lived-session checkpoint = **RE-SCOPED** (freshstart already live; real gap = intra-day bloat) — dispositioned with correction; ⑤ media caps = **DONE (applied+measured baseline)**; ⑥ Porsche = **surfaced** (insurance-not-warranty), re-deliver **CARRIED**; ⑦ payments F26 / reply-watcher / generate-mode cred / Battery = **CARRIED** (Stage-2, by design). Nothing silently dropped.

## Carried → 6c.24 (in the opener)
- **Aggressive core restructure + ONE billed fresh-session Marie turn** — the still-open gate signal (recall=0): move verb-tables to retrieval-only, then verify she searches + follows hot rules + clears Porsche in one turn (real Opus path).
- **Media-caps before/after** — read the canary over coming days as real browser/PDF turns exercise `mode:efficient` (the F26 evidence the caps help).
- **Intra-day :main-bloat durable fix** — a threshold-triggered reset beyond the 06:55 daily (the 1.43M-from-one-turn source).
- **Dreaming-on** — still blocked: no `DREAMS.md` exists yet (`Dreaming: off`, diary absent). Enable (cheap model, cron) then review first output → then the §8 week-clock can start.
- **Porsche re-deliver** (fresh, browser-bounded) + **doctor hygiene** (23 orphan transcripts, 1 stale agent dir).
- **Stage-2:** payments real-charge F26, reply-watcher, generate-mode cred store, Battery re-run.

## Post-close follow-up (Peter, same session — see [P6-120] UPDATE)
Peter: proceed with the surfaced items **but** stop on Porsche + **don't do quality-impacting changes — flag for Andreas.** Actioned: (1) **Porsche finding RETRACTED** — I conflated "Porsche Approved" (the certified *pre-owned* programme) with the verb "approved"; his car is new → the windscreen=insurance-not-warranty conclusion was void. Purged the false note from Marie's `memory/2026-07-05.md` + re-indexed (no longer retrievable). (2) **Aggressive core-slice + dreaming = ring-fenced to Andreas** (not attempted; reinforced in `PRE-COS-BACKLOG §A.4` items 3/5); the billed verify turn dropped from the interim track. (3) **Doctor:** `openclaw sessions cleanup` ran (store maintenance; live sessions intact) — orphan transcript FILES (~144 MB) + stale agent dir need `rm` (deny-listed) → flagged for Peter. openclaw.json UNCHANGED by the follow-up (`eb08cfa7d7b6`). The 6c.24 opener was revised accordingly (aggressive slice/dreaming moved to a ⛔-flag; interim = measure + bound only).
