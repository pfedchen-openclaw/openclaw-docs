# SESSION 6c.18 — Overnight verdict + [P6-93] root-caused AND fixed + Charlotte's breakdown resolved — CLOSURE
_2026-07-03. Started SSH (recon/diagnosis/decisions), finished **GUI/Screen-Sharing** (Peter corrected: Terminal via Screen Sharing, not SSH — so D20 ops were available and I continued rather than deferring). Shipped: first real overnight verdict read + verifier fixed; **[P6-93] "she forgets" root-caused to the bootstrap injection budget AND fixed + verified**; **Charlotte's overnight breakdown diagnosed + resolved**. New codes **[P6-96/97/98]**. `openclaw.json` **CHANGED** (`8a6e2a19d9d5`→`82a293d97aac`/10097B — the bootstrap raise; A-7 re-anchored, last-good synced). **One Sonnet smoke-test turn** (sanctioned single test)._

## Stage 0 — done
- **A-7:** was `8a6e2a19d9d5`/10096B → **now `82a293d97aac`/10097B** (bootstrap raise), 600/staff, last-good synced post-verify. Pin 2026.4.22. No sandbox/pin change.
- **★ Overnight verdict ([P6-96]):** one-shot **fired as designed** 07:15 03-Jul, reached Peter (msg 947), **self-removed**. Its PARTIAL (3/6) was WRONG — 3 false-negative greps (em-dash `.`-byte ×2; multi-line RESET). Automation **actually PASSED 6/6** (suppress/restore/06:55 dual reset+restart+sentinel/archives). Greps fixed → PASS 6/6, committed (config repo `25077c9`). **BUT this only proved the plumbing — see [P6-98] blind-spot.**
- **TOOLS-slim SHAs anchored, no capability lost;** PA-PLAYBOOK drift expected (P6-95 §credentials). Verbs grep-present.

## What shipped (chronological)
1. **[P6-96] overnight verifier fixed + committed** — reference artifact scores correctly.
2. **[P6-97] [P6-93] ROOT-CAUSED** — not the never-reset theory: a single bootstrap **injection budget** (`agents.defaults.bootstrapTotalMaxChars`, was 75000) consumed in file order `AGENTS→SOUL→TOOLS→IDENTITY→USER→HEARTBEAT→MEMORY`; overflow → later files **skipped**. The per-injection residual varies (695→8438) because the budget shares the pool with session state. This is the "she forgets" mechanism.
3. **Decisions taken (AskUserQuestion):** bootstrap = **both slim + raise**; card = **host-mediated op-get** (F70 resolved, card already in 1Password); battery = **hold until budget + card land**.
4. **[P6-98] Charlotte's overnight breakdown diagnosed + FIXED + verified** (after Peter flagged it):
   - **Blind-spot:** the overnight-verifier AND the heartbeat-watchdog only check beats **fire**, never that work **happens** — both were green while Charlotte accomplished nothing. → Andreas: a function-level morning check.
   - **Two coupled faults:** (a) her 08:16 fresh-session beat ran on a **truncated** bootstrap (missing TOOLS/behaviour) → didn't sweep; (b) her 12:16 archive attempt was **refused by the sandbox** (shell `for`-loop preflight + `/tmp` escape) because TOOLS.md only showed single-id archive.
   - **Fix (GUI):** raised `bootstrapTotalMaxChars` **75000→110000** (F17 via `scripts/stage-bootstrap-raise.py`; validated single-key change), gateway restarted; added a **batch-archive pattern** to pro TOOLS.md.
   - **VERIFIED (F26):** zero truncation on any injection post-raise; one Sonnet smoke-test → Charlotte reproduced **both** the batch-archive fix **and** the late-loading `calendar-prepare` command (proof her full TOOLS.md now injects). **Root fixed + demonstrated.**

## End-state (anchors)
- **`openclaw.json`: `82a293d97aac`/10097B** — `bootstrapTotalMaxChars` 75000→110000; A-7 clean (live==last-good), 600/staff. Rollback: last-good was `8a6e2a19d9d5` pre-verify.
- **Config repo:** `overnight-verify.sh`→`9186db494779` (commit `25077c9`); new `scripts/stage-bootstrap-raise.py` + `scripts/sync-lastgood.py` (to commit at close).
- **Live-bind brief (not repo-tracked):** pro TOOLS.md `df7cfee555f6`→`44ddafb744fb`/13466B (batch-archive line); backup in scratch `6c18-brief-backup/`.
- **Deviations:** [P6-96/97/98] appended additive → log `<re-anchor at commit>` (docs repo).
- **Cost:** 1 Sonnet gateway smoke-test (sanctioned single test, immaterial). No loops/Opus/batches.

## Carried → 6c.19 (GUI, Peter-paced)
- **★ LEAD — Marie's capped card via host-mediated op-get** + Peter-supplied-credential actuation ([P6-95] item 1): host `op-get` at the pre-payment screen / one-time Chrome-autofill save; 2FA-in-Revolut pause; Finance log; graduate the payment posture. Irreversible first-use → confirm before the first live charge. Card already in 1Password/OpenClaw.
- **Battery re-run — now UNBLOCKED** (briefs inject fully): cheapest model, ~$5 cap; convert T3/T6/digest FAIL→PASS via real Telegram. **T1:** confirm the rental reminder fired 19:00 Fri 3-Jul.
- **Monitor the truncation canary** stays clean across a full day incl. tomorrow's 06:55-reset morning beats (the real test of Charlotte's next sweep). **Optional slim** (reduce per-beat injected tokens) — no longer urgent; mirror the batch-archive line to pa TOOLS.
- **Andreas backlog (new):** function-level morning check (not beat-liveness); `:telegram:direct` compaction-pressure ([P6-98]); plus the standing items.

## Honest note
Peter's two corrections turned this from a tidy close into the real fix. The overnight "PASS 6/6" was true but **shallow** — it verified the night/morning plumbing, not that the agents worked, so it sat on top of Charlotte's actual breakdown without flinching (same blind-spot as the watchdog). Chasing *why* she broke down led straight back to [P6-93]: her fresh morning session was truncated, so she booted without her tools; and when she did try, the briefs never taught her a sandbox-legal way to archive in bulk. Both are now fixed at the root — the budget raised and verified by a real turn (she can see her whole toolset again), the archive pattern corrected and echoed back to me in her own words. What I did **not** do is over-claim: the raise is a cap (bounded cost), the slim is deferred as optional, and tomorrow's 06:55 morning is the honest next proof that the fix holds through a real reset — noted for monitoring, not asserted.

---
_Attachments for 6c.19: this closure + `SESSION-6c.19-OPENER.md` + `PHASE-6-DEVIATIONS-LOG.md` (through P6-98) + `ACCEPTANCE-GATE-SCORECARD.md`._
