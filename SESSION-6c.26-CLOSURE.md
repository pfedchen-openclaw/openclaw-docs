# SESSION 6c.26 — CLOSURE

_Reply-watcher activation (Peter-gated) + 6-Jul health-review fixes + 0b F26 confirm + reset-net/media observation. Peter widened the run mid-session (ride-the-context: stop closing at every task boundary — [[ride-context-dont-close-early]]) and authorised one Opus turn for the 0b confirm. GUI/Aqua (D20 satisfied — `SSH_CONNECTION` empty, `gui/502` reachable). openclaw.json **UNCHANGED** (`eb08cfa7d7b6`/10654B — no config edit, no gateway restart; A-7 unbroken)._

## What this session did
1. **Stage-0 re-verify — all GREEN ([P6-126]).** Applied-lever A-7 (mode=efficient, contextTokens=120000, pin 2026.4.22/00bd2cf); deviations gap-free; reset-net scripts byte-match; watchdog DRYRUN healthy true-negative; 3 LaunchAgents active+exit0; reply-watcher staged-only at start; memory index pa 35·125 / pro 31·36 (Dirty no, Dreaming off, recall pa 12·0).
2. **★ Reply-watcher ⑧ — DRYRUN activated + verified live at £0 ([P6-126]).** Peter chose **Dryrun bootstrap**. Bootstrapped + kickstarted the dryrun-pinned plist; the live-cred Gmail poll ran **under launchd** (F21/F51-clean), exit 0, **2 real matches** (Servane "Your arrival in Paris"; Greg Molter "Follow Up"), no promo noise. Zero `openclaw` calls, zero spend, no send, SEEN not recorded. Now **live in dryrun (900s)**; notify-flip = Peter's next go.
3. **★ 0b — exec-preflight fake-done fix + F26 CONFIRMED PASS ([P6-126]).** Root cause: Charlotte's TOOLS.md already taught the direct-`python3 /workspace/x.py` form; **Marie's didn't** → the 6-Jul fake-done. Added the note to `workspace-pa/TOOLS.md`. **F26 (1 authorised Opus turn, `claude-opus-4-7`, fresh session, no `--deliver`):** on the exact P6-125 trigger (7-day calendar), Marie returned a complete accurate rundown from **real** data — **zero exec-preflight refusals, zero fabrication**, approve-first judgement (flagged the triple-booked Josephine entry, asked before cleanup). Injection report confirmed the edited brief loaded. **Reliability loop closed.**
4. **★ Brief-injection ceiling — found + fixed ([P6-126]).** The F26 revealed pa's TOOLS.md sat at the exact injection ceiling (`bootstrapTotalMaxChars=110000`; fit at 15765 chars); the note pushed it over, silently clipping the browser commit-verb syntax. **Peter chose "relocate + tighten":** moved the verb syntax to `reference-tools.md` (indexed), kept the commit-safety reminder + tightened the note → TOOLS.md **15745 < 15765, clip-free**; index `Dirty:no`. Andreas: pa's brief is at budget ceiling.
5. **generate-mode credential store ([P6-115]) — inspection-verified end-to-end; live-proof walled off, carried ([P6-126]).** `cred-save.sh` generate dry-run + `op-put.sh --generate-password` + secret hygiene all confirmed; existing-mode guard refuses passwordless (rc2). Peter authorised a throwaway write, but **`op vault list` is deny-listed to this session** (op mutation is launchd-drain/host-bridge territory, F21/F51) — did **not** route around it; carried. Zero residue.
6. **0e — cred-save log-spin fixed ([P6-126]).** `cred-save-drain.sh:94` idle branch silent (`exit 0`); `zsh -n` clean. `4e714117`→`cfc7a3d0`.
7. **0a — compaction lever HELD for Andreas ([P6-126]).** Peter's choice, reinforced by A-12: **no `compaction.timeout` key exists**. Fired once (6-Jul). No config change, no restart.
8. **Observation — reset-net still armed, not-yet-fired (good case); media after-window opening (partial) ([P6-126]).** No `reset applied` in the log; 07-06 archives are the freshstart's. Pre-caps `96869d34` rolled out of pa's window → median −22% / p95 −17%; canary 07-06 data lands tonight; §8 not recalibrated.

## Cost (Console-authoritative)
- **1 authorised Opus turn** (0b F26 confirm) — immaterial; Peter explicitly waived never-Opus for this single run after A-12 showed a faithful Sonnet-on-Marie confirm is impossible (pa pinned Opus; gateway has no `--model`; embedded is the false-negative path).
- Reply-watcher dryrun = poll+log only (£0). Everything else inspection / free CLI. No config change, no restart, no loops.

## SHA anchors (changed this session)
| file | before | after |
|---|---|---|
| `workspace-pa/TOOLS.md` (0b note + relocate/tighten; live, outside docs git) | `eef039c7a89e`/15939B | `ab2eb956582b`/15924B |
| `workspace-pa/memory/reference-tools.md` (relocated browser verbs) | (2871B) | `b76028131e5a`/3894B |
| `scripts/cred-save-drain.sh` (0e) | `4e714117e36c`/5158B | `cfc7a3d07b01`/5108B |
| `scripts/ai.openclaw.reply-watcher.plist.staged` → copied live to `~/Library/LaunchAgents/` | — | `d3ed81e12992`/1858B |
| `_session-docs/PHASE-6-DEVIATIONS-LOG.md` | (P6-126 + updates) | — |
- **openclaw.json UNCHANGED** — `eb08cfa7d7b6`/10654B (A-7 clean).
- **New live LaunchAgent:** `ai.openclaw.reply-watcher` (dryrun, 900s) — loaded gui/502, last exit 0.

## Carry-chain check ([P6-101])
- **Mechanical:** P6-1…126 gap-free (`missing []`), no dangling refs; only header dupes = historical 21/22. PASS.
- **Carry diff vs 6c.25 "Carried → 6c.26":** reply-watcher activation → **DONE (dryrun live + verified)**; reset-net F26 → **observed not-fired (carried)**; media after-window → **partial (carried)**; battery T3/T1/T4/T5 → **not touched, Peter-gated (carried)**; Wikimedia payment → **not touched, Peter's go (carried)**; generate-mode cred store → **inspection-verified; live-proof walled-off (carried)**; ANDREAS-ONLY → **untouched (carried)**. Plus in-session: **0b F26 PASS + brief-ceiling fix** (new, complete). Nothing silently dropped.

## Carried → 6c.27
- **Reply-watcher notify-flip** (Peter's go): after ~1 day of dryrun match-rate, flip `REPLYWATCH_MODE=notify` + re-bootstrap; poke only when scope is tight.
- **generate-mode live-proof** — a launchd-drain-actioned request or a Peter-run `op item create`+verify+delete (my session is op-mutation-walled; chain already inspection-verified).
- **Brief-budget (Andreas)** — pa's TOOLS.md is at the 15765-char injection ceiling; decide what earns always-injected vs retrieval.
- **Reset-net production F26** — watch for the first genuine intra-day `:main` breach (free inspection).
- **Media-caps after-window** — read `daily.log` as 07-06+ `mode:efficient` rows accrue; recalibrate §8.
- **Battery** — T3 (Marie/Opus) via Peter's Telegram or authorised; T1 reliability; T4/T5 capability/payment wall.
- **Payment** — Wikimedia £10 donation on Peter's go (prepare-and-surface).
- **⛔ ANDREAS-ONLY (do NOT attempt):** aggressive frozen-core slice + its billed verify turn; dreaming-on; week-clock; the `:telegram:direct` compaction bounding + brief-budget policy. Unchanged.
