# SESSION 6c.26 — CLOSURE

_Reply-watcher activation (Peter-gated) + 6-Jul health-review fixes + reset-net/media observation. GUI/Aqua (D20 satisfied — `SSH_CONNECTION` empty, `gui/502` reachable). Peter answered both embedded decisions live: **Dryrun bootstrap** for the reply-watcher, **Hold for Andreas** on compaction. openclaw.json **UNCHANGED** (`eb08cfa7d7b6`/10654B — no config edit, no gateway restart; A-7 unbroken)._

## What this session did
1. **Stage-0 re-verify — all GREEN ([P6-126]).** Applied-lever A-7 (mode=efficient, contextTokens=120000, pin 2026.4.22/00bd2cf); deviations P6-1…125 gap-free; reset-net scripts byte-match; watchdog DRYRUN healthy true-negative; 3 LaunchAgents active+exit0; reply-watcher staged-only; memory index pa 35·125 / pro 31·36 (Dirty no, Dreaming off, recall pa 12·0).
2. **★ Reply-watcher ⑧ — DRYRUN activated + verified live at £0 ([P6-126]).** Peter chose **Dryrun bootstrap**. `cp` staged plist → `~/Library/LaunchAgents/` (byte-identical `d3ed81e12992`) → `launchctl bootstrap gui/502` + `kickstart -k`. The live-cred Gmail poll ran **under launchd** (F21/F51-clean — this ops session never caused an `agent-creds/**` read), exit 0, **2 real matches** in the default scope ("Your arrival in Paris"/Servane Rangheard; "Follow Up"/Greg Molter) with **no promo/newsletter noise** — category exclusions working. mode=dryrun → zero `openclaw` calls, zero spend, no Telegram send, SEEN not recorded, err log clean. ⑧ now **live in dryrun (900s)**. Notify-flip = Peter's next go.
3. **★ 0b — exec-preflight fake-done root cause fixed ([P6-126]).** Found the real cause: Charlotte's TOOLS.md **already** taught the direct-`python3 /workspace/x.py` invocation form (line 49); **Marie's had no equivalent** → she hit the guard on 6-Jul and fabricated a draft-done. Added a generalised note to `workspace-pa/TOOLS.md` (single direct invocation; loops/templated bodies → a `/workspace` script; if exec still refuses, **surface — never fabricate a receipt**). pa `eef039c7`→`a4d77824` (+812B); pro untouched. **Billed Sonnet F26 confirm deferred to Peter's cost-OK.**
4. **0e — cred-save log-spin fixed ([P6-126]).** `cred-save-drain.sh:94` idle branch made silent (`exit 0`) — stops the 168KB/4,554-line unbounded `cred-save-watch.out.log` accretion. `zsh -n` clean. `4e714117`→`cfc7a3d0`.
5. **0a — compaction lever HELD for Andreas ([P6-126]).** Peter's choice, reinforced by A-12: **no `compaction.timeout` config key exists**; the [P6-125] 60 s×4 is a gateway/model-call timeout, not a compaction knob, so "raise the timeout" isn't a clean lever. Fired once (6-Jul). No config change, no restart.
6. **item 2 — reset-net: still armed, not-yet-fired (good case) ([P6-126]).** No `reset applied` in the log; the 07-06 archive dirs are the 06:55 daily **freshstart's**, not an auto-net fire. Kept watching; did not synthesise a breach.
7. **item 3 — media-caps after-window opening (partial) ([P6-126]).** Pre-caps `96869d34` rolled out of pa's window → median 229180→177685 (−22%), p95 288733→239411 (−17%); `retrieval_uses:0` still. Canary runs late-night → 07-06 "after" lands tonight. §8 not recalibrated (partial data).

## Cost (Console-authoritative)
- **Zero billed agent turns.** Reply-watcher activation was dryrun (poll+log only, £0). All else was inspection / free CLI. No config change, no restart, no loops. (0b's billed F26 confirm was **deferred**, not run.)

## SHA anchors (changed this session)
| file | before | after |
|---|---|---|
| `workspace-pa/TOOLS.md` (0b, live; outside docs git) | `eef039c7a89e`/15939B | `a4d77824918c`/16751B |
| `scripts/cred-save-drain.sh` (0e) | `4e714117e36c`/5158B | `cfc7a3d07b01`/5108B |
| `scripts/ai.openclaw.reply-watcher.plist.staged` | — | `d3ed81e12992`/1858B (now also copied live to `~/Library/LaunchAgents/`) |
| `workspace-pro/TOOLS.md` | `44ddafb744fb` (unchanged) | `44ddafb744fb` |
| `_session-docs/PHASE-6-DEVIATIONS-LOG.md` | (append P6-126) | — |
- **openclaw.json UNCHANGED** — `eb08cfa7d7b6`/10654B (live==last-good; A-7 clean).
- **New live LaunchAgent:** `ai.openclaw.reply-watcher` (dryrun, 900s) — loaded gui/502, last exit 0.

## Carry-chain check ([P6-101])
- **Mechanical:** P6-1…126 gap-free (`missing []`), no dangling refs; only header dupes are the known-historical 21/22 (leave). PASS.
- **Carry diff vs 6c.25 "Carried → 6c.26":** reply-watcher activation → **DONE (dryrun-activated + verified; notify-flip carried)**; reset-net F26 → **observed not-fired, kept watching (carried)**; media-caps after-window → **partial after observed, §8 held (carried)**; battery T3/T1/T4/T5 → **not touched, Peter-gated (carried)**; Wikimedia payment → **not touched, Peter's go (carried)**; generate-mode cred store → **not touched (carried)**; ANDREAS-ONLY (slice/dreaming/week-clock) → **untouched (carried)**. Nothing silently dropped.

## Carried → 6c.27
- **Reply-watcher notify-flip** (Peter's go): after ~1 day of dryrun match-rate observation, flip `REPLYWATCH_MODE=notify` in the live plist + re-bootstrap; then poke only when scope is tight. Real agent-side "awaited-thread" scoping stays Andreas-scale.
- **0b billed F26 confirm** — a single cheap **Sonnet** turn (Peter cost-OK) to confirm the pa TOOLS.md exec-form note took (Marie invokes helpers as `python3 /path/file.py` under the guard).
- **Reset-net production F26** — still watching for the first genuine intra-day `:main` breach to self-heal (free inspection when it happens).
- **Media-caps after-window** — read `daily.log` once the 07-06+ `mode:efficient` rows accrue; recalibrate §8 off the real "after".
- **Battery** — T3 (Marie/Opus) via Peter's own Telegram or an authorised Sonnet-equivalent; T1 reliability; T4/T5 capability/payment wall (autonomy decision).
- **Payment** — Wikimedia £10 donation teed up as a real payments-F26 on Peter's go (prepare-and-surface).
- **generate-mode credential store** — round out [P6-115] (only `existing` proven live).
- **⛔ ANDREAS-ONLY (do NOT attempt):** aggressive frozen-core slice + its billed retrieval-usage verify turn; dreaming-on; the week-clock. Unchanged.
