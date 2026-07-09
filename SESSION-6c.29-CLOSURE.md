# SESSION 6c.29 — CLOSURE

_Peter-driven billed re-tests on the healed lean session + two live-caught defects fixed. GUI/Aqua (D20 — `SSH_CONNECTION` empty, `gui/502` reachable). **openclaw.json UNCHANGED all session** (`3267edd8b6aa`/10489B, live==last-good, A-7 clean). My host-side work £0 billed; the T3/3-task turns ran on Marie's live Opus (Peter-authorised real-chat path). Findings [P6-133]/[P6-134]/[P6-135]._

## What this session did
1. **Stage 0 GREEN + steady-state clean.** A-7 `3267edd8b6aa`/10489B 600/staff; pin 2026.4.22 (00bd2cf); levers `contextTokens=120000` + `browser.snapshotDefaults.mode=efficient`; deviations **P6-1…132 gap-free**; all briefs sha-matched the opener; plists live==staged; LaunchAgents exit0; gateway pid 80509. **chat-bloat-reset steady-state: SKIPped ×3 since the 6c.28 heal (`no-chat-session`/`not-bloated`) — no restart loop.** reply-watcher notify clean (err/out 0 lines).
2. **★ T3 responsiveness — PASS (organic) [P6-135].** Peter was live with Marie throughout (the CeFoLiAc thread); prompt replies, no stick. The [P6-129] pathology is absent on the lean session.
3. **★ 3-task enumeration — PASS [P6-135].** One message → three distinct asks; all handled one-by-one, none merged/dropped; "review Q3 deck" persisted a quality `TASKS.md` row (36→37, acceptance ts + proof-of-done + resurface cadence); zero err.log deltas. Validates the 6c.28 multi-task brief note.
4. **★ Wikimedia £10 — donor-flow PASS, card-injection structurally blocked [P6-135].** Marie drove to pre-pay (£10 + Peter's details) and **correctly stopped at the card slot** (prepare-and-surface held). But the card fields are **Gr4vy cross-origin hosted-field iframes** (3 separate targets) — `pay-fill.sh`'s single-`--target-id` model can't reach them. That's [P6-130] Andreas deep-robustness, not a one-off patch on a live charge. Abandoned cleanly (Peter had already donated; £0 charge). Also confirmed `refs=aria`+`efficient` snapshot fails on the current backend (Playwright-launched-Chromium swap evidence). **T4-shaped win:** drive-to-checkout + surface-charge + never-auto-pay demonstrated on a real site — the 6c.17 iGolf L1 wall is cleared.
5. **★ [P6-133] memory-flush `.append` rejection FIXED.** Marie's durable-lesson writes were bouncing (`memory/<date>.md.append` rejected by the flush guard; 7 rejections, 4 the daily-`.append` pattern). Root = `AGENTS.md` L42 "(or use the append verb)" mis-executed as a path suffix. L42 clarified; injects next session; verify = no new `.append` rejections.
6. **★★ [P6-134] CeFoLiAc — fabrication hypothesis REFUTED; silent cc-drop tool bug fixed.** Peter reported the follow-up missing + suspected invention; Marie's own 12:41 note self-accused. **Both wrong** — two genuine `toolResult` records prove a real `send-draft` (`messageId 19f46a0b5109dfea`, `sent:true`) confirmed in SENT. The **mirror rule ([P6-103]) worked** — it stopped her wrongly confessing. Peter couldn't see it because `gmail_helper`'s single-valued `--cc` **silently dropped him** (repeated `--cc pfedchen@ --cc olga@` → last-wins → Olga only). Fixed: `--to/--cc` → `action="append"` + normaliser (dry-run proven both survive), deployed live to the sandbox bind source; recipient-verify brief note added.

## Cost
**£0 billed on my side** (host-side trajectory forensics, dry-runs, log/sha inspection, docker inspect). The T3/3-task re-tests consumed Marie's live Opus turns on the real Peter-chat path (F26-valid; Peter-authorised "all three"). Wikimedia: **£0** (abandoned before submit). `docker exec` into the pa sandbox was **denied** — verified recipients from the trajectory instead, did not route around.

## SHA anchors
| file | before | after |
|---|---|---|
| `openclaw.json` | `3267edd8b6aa`/10489B | **UNCHANGED** `3267edd8b6aa`/10489B (last-good synced, A-7 clean) |
| `workspace-pa/AGENTS.md` (P6-133 L42 + P6-134 L21 notes) | `f68b9fc5bc38`/16526B | **`8ab05e5f7838`/16673B** (+147B; 1B under the 16674B proven-safe ceiling) |
| `agent-creds/gmail_helper.py` (bind source = container `/opt`) | `f2ed9ce6caaa`/13190B | **`f14489444597`/13596B** (agent-tools mirror synced identical) |
- **Backups:** `workspace-pa/AGENTS.md.pre-6c29-p6133` (`f68b9fc5bc38`), `scripts/gmail_helper.py.pre-6c29-p6134` (`f2ed9ce6caaa`).
- **Live state:** gateway running (pid 80509); Marie session lean; chat-bloat-reset cooldown-armed + steady-state SKIP; reply-watcher notify + reset-net all loaded/exit0.

## Carry-chain check ([P6-101])
- **Mechanical:** P6-1…135 gap-free, no dangling refs. PASS.
- **Carry diff vs 6c.28 "Carried → 6c.29":** T3 billed re-test → **DONE (PASS, organic)**; Wikimedia billed re-test → **donor-flow DONE (PASS); card-injection blocked → [P6-135]/[P6-130] Andreas**; controlled 3-task → **DONE (PASS)**; observe chat-bloat steady-state → **DONE (clean, no loop)**; compaction/bloat ROOT → **Andreas (unchanged)**; hard pulse send-gate [P6-128] → **still open, Andreas**; v7 capstone → **carried (overdue)**; ANDREAS-ONLY set → **untouched**. New this session: [P6-133] memory-fix (DONE), [P6-134] cc-drop (DONE). Nothing silently dropped.

## Carried → 6c.30
- **★ Verify the P6-133 + P6-134 brief fixes injected + holding** — they take effect on Marie's next compaction/session. F26 signals (free inspection): **no new `.append` rejections** in err.log; a real **multi-cc send lands BOTH recipients**. No billed turn needed to confirm either.
- **★ Acceptance gate — much closer.** Today cleared T3 (PASS) + the T4 drive-to-checkout-and-surface shape (PASS on a real site). **Remaining:** **T5** account-create (hard L1 wall — a Peter *autonomy decision*, not a build), **T1** timed-reminder reliability re-test, and the **memory-v2 reliability gate** (zero-truncation / retrieval-hit / cost-under-target). See ACCEPTANCE-GATE-SCORECARD.
- **★ [P6-135]/[P6-130] — hosted-field payment robustness** (Gr4vy/Stripe-Elements/Braintree: card fields in cross-origin iframes) → Andreas cross-site remit §C. `pay-fill.sh` needs a multi-target / per-field-iframe extension; the current single-`--target-id` model only fits single-page card forms. + the `refs=aria`/`efficient` backend gap → Playwright-launched-Chromium swap.
- **v7 CAPSTONE / pre-6d consolidation ([P6-76]/[P6-127]) — OVERDUE**, the milestone gate before Andreas. Canonical docs stale since 2026-06-11.
- **⛔ ANDREAS-ONLY (unchanged):** aggressive frozen-core slice + verify turn (the bloat/stick ROOT); dreaming; `:telegram:direct` compaction bounding (`compaction.model`/`keepRecentTokens` tunable); brief-budget policy; Playwright backend swap; cross-site browse/forms/payment robustness ([P6-130] §C, incl. the benign browser-allowlist warning); hard code-level pulse send-gate ([P6-128]).
