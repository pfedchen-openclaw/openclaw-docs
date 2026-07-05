# SESSION 6c.25 — CLOSURE

_Memory-v2 interim (cont.): observe the reset net in production + media-caps after-window + doctor-hygiene hand-off. Peter mid-session authorised the non-payment Stage-2 billed items ("do everything except payment, unless a £10 Wikimedia donation is easy"). GUI/Aqua (D20 satisfied). openclaw.json **UNCHANGED** (`eb08cfa7d7b6`/10654B — no config edit this session; A-7 unbroken)._

## What this session did
1. **Stage-0/1 observation — all GREEN, recorded ([P6-123]).** Deviations P6-1…124 gap-free; reset-net scripts byte-match the opener; watchdog DRYRUN vs the real store = healthy true-negative; 3 LaunchAgents active; media caps + compaction levers all live; `toolResultMaxChars` unset; memory index pa 35/35·125, pro 31/31·36 (Dirty no, Dreaming off, recall 12·0).
2. **Reset-net production status — armed, NOT yet fired on a real breach (the good case) ([P6-123]).** The only reset-path log activity (16:20–16:22, 05-Jul) is 6c.24's own [P6-121] F26 **fixtures** (`inputTokens=300000` constant, `[DRYRUN]` markers, sentinel content `0`); **`reset applied` appears nowhere**, no 16:20 archive, real `pa:main` (`831bded7`) unchanged at 41k/120k. Today's real ~1431K bloat (14:46) was cleared by a manual/scheduled freshstart (14:57 archive), not the auto-net. Kept watch; did NOT synthesise a breach.
3. **Media-caps after-window still closed ([P6-123]).** All `daily.log` entries are same-calendar-day as 6c.23 close; pa median/p95 frozen (pre-caps `96869d34` dominates). §8 thresholds kept. Carried as a read.
4. **Doctor-hygiene holds ([P6-123]).** Sessions dirs still lean (pa 38M / pro 4.2M).
5. **Battery T6 (Charlotte triage) FAIL(4/10)→PASS ([P6-123]).** One Sonnet Gateway turn; check-Sent + verify-before-surface + don't-act-unprompted remediations all demonstrably hold. Scorecard updated. Clears the judgement blocker for T6.
6. **Reply-watcher (⑧) BUILT + logic-verified; staged, not bootstrapped ([P6-124]).** `scripts/reply-watcher.sh` (`7763696a30f8`/5840B) — bounded Gmail poll on the agents' live cred (by PATH), dedup, `dryrun`(default)/`notify`(free)/`poke`(billed, capped) modes. Plist staged (`d3ed81e12992`/1858B, dryrun-pinned), NOT loaded. **Incident:** the notify/poke "offline" test wasn't offline (a zshenv re-derives PATH → the real `openclaw` ran) → 3 spurious Telegram sends + 1 real billed **Opus** poke on Marie; corrected (msg 1114); lesson saved to memory. Silver lining: the poke path is now proven end-to-end.
7. **Payment — surfaced, NOT executed ([P6-124]).** A real £10 Wikimedia donation is not "easy" (irreversible, no card, needs Peter's 2FA, billed browser-heavy Opus turn) → prepare-and-surface. Ready to fire on Peter's go.

## Cost (Console-authoritative)
- 1 authorised Sonnet turn (T6 re-test) — immaterial.
- **1 UNINTENDED Opus `--deliver` turn** (reply-watcher test-harness leak) — within Peter's session authorisation, but wrong model + accidental; disclosed honestly ([P6-124]).
- Everything else was inspection / free gateway-bot sends. No config change, no restart, no billed loops.

## SHA anchors (changed/added this session)
| file | sha256-12 | bytes |
|---|---|---|
| `scripts/reply-watcher.sh` (new) | `7763696a30f8` | 5840 |
| `scripts/ai.openclaw.reply-watcher.plist.staged` (new) | `d3ed81e12992` | 1858 |
| `_session-docs/PHASE-6-DEVIATIONS-LOG.md` | (append P6-123/124) | — |
| `_session-docs/ACCEPTANCE-GATE-SCORECARD.md` | (T6 re-run row + 6c.25 addendum) | — |
| memory: `no-path-stub-openclaw-in-tests.md` (new) | — | — |
- **openclaw.json UNCHANGED** — `eb08cfa7d7b6`/10654B (live==last-good; A-7 clean). No config edit, no gateway restart.

## Carry-chain check ([P6-101])
- **Mechanical:** P6-1…124 gap-free, no dangling refs (re-verified post-edit).
- **Carry diff vs 6c.24 opener Stage-1/2:** (1) reset-net production F26 → **observed: not-yet-fired, good case, kept watching** (not dropped); (2) media-caps before/after → **read: after-window still closed, thresholds kept** (carried, honest); (3) doctor-hygiene → **confirmed holding** (closed at 6c.24). Stage-2: reply-watcher → **built + surfaced** (was carried); battery re-run → **T6 done (PASS)**, T3 consciously deferred (Opus); payments → **surfaced** (Peter deferred); generate-mode cred store → **not touched** (was NOT in Peter's "everything except payment" set — his AskUserQuestion options were close/payments/reply-watcher/battery; carried forward explicitly). Nothing silently dropped.

## Carried → 6c.26
- **Reply-watcher activation** (Peter's call): pick scope query + mode (`notify` free / `poke` billed-capped), then bootstrap the staged plist. Real agent-side "awaited-thread" scoping stays Andreas-scale.
- **Reset-net production F26** — still watching for the first genuine intra-day `:main` breach to self-heal (free inspection when it happens).
- **Media-caps after-window** — read `daily.log` over coming days once real `mode:efficient` browser/PDF turns land; recalibrate §8 off the real "after".
- **Battery** — T3 (Marie/Opus) re-test via Peter's own Telegram or a future authorised Sonnet-equivalent; T1 reliability re-test; T4/T5 capability/payment wall (autonomy decision); organic sample.
- **Payment** — Wikimedia £10 donation teed up as a real payments-F26 on Peter's go.
- **generate-mode credential store** — round out [P6-115] (only `existing` proven live).
- **⛔ ANDREAS-ONLY (do NOT attempt):** aggressive frozen-core slice + its billed retrieval-usage verify turn; dreaming-on; the week-clock. Unchanged.
