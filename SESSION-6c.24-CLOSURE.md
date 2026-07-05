# SESSION 6c.24 — CLOSURE: intra-day `:main`-bloat auto-reset net shipped (watchdog → archive-first per-agent freshstart, human-chat-guarded), media-caps after-window measured (not yet open), doctor orphan-detection corrected

_Closed 2026-07-05 (GUI/Aqua, D20 satisfied — `SSH_CONNECTION` empty, `gui/502` reachable). Memory-v2 **interim** session per the 6c.24 opener: measure + bound only; the aggressive core-slice + dreaming stayed ⛔ ring-fenced to Andreas (not attempted). New codes: **P6-121** (build), **P6-122** (measurements). openclaw.json **UNCHANGED** (`eb08cfa7d7b6`/10654B, A-7 clean — no config edit this session)._

## One-line result
The 06:55 freshstart bounded only *overnight* `:main` bloat; 6c.24 wired the existing 250K watchdog to a **self-heal** — on a `:main` per-turn breach it now runs an archive-first `session-freshstart --force --agent <id>`, **guarded so it never restarts the gateway under a live human chat** — built, DRYRUN-verified end-to-end through the real scripts, and **live on the next 15-min tick** (no plist/restart/billed-turn needed). Media-caps before/after could not be produced (canary after-window hasn't opened — same calendar day, no post-caps browser turns); doctor orphan-detection was corrected (naive match is unsafe) and a safe 41-file/206 MB manifest staged for Peter.

## What landed
1. **Intra-day `:main` auto-reset net (P6-121) — the session's lead, DONE + verified.**
   - `session-freshstart.sh`: **`--agent <id>` selector** added (shift-loop parse, validates ∈{pa,pro}); single-agent scope so one breach doesn't reset the healthy agent. **Backward-compatible — no flag → both agents** (06:55 job unchanged). `fc75bc0d5133`/6009B → **`55020ef7acd6`/6637B**.
   - `heartbeat-watchdog.sh`: **section (D)** reads each `agent:<id>:main`, emits `RESET|<agent>|<inputTokens>` iff **inputTokens ≥ 250K** (the recurring per-turn window cost — NOT cumulative totalTokens) AND `:main` is live (<6h) AND **no `:telegram:direct` touched <10m** (else DEFER). **`run_reset()`** runs `session-freshstart --force --agent <id>`, per-agent **90-min cooldown sentinel** (records only on confirmed `reset applied`), **one reset/cycle**, and **skips its own stall check** after a reset. `2e93b6c40910`/14313B → **`183896a33dd2`/20438B**.
   - **F26 (DRYRUN + fixtures, no send/restart/reset):** `--agent` selection (pa-only / both / bad→exit2); section (D) breach→RESET, human-active→DEFER, stale→silent, below→silent; cooldown 30m→skip / 120m→proceed; full real-store DRYRUN → clean, healthy `:main` triggers nothing. `zsh -n` clean.
   - **Live-on-next-tick:** no plist change → the LaunchAgent re-reads the edited script at its next 900s exec; no `bootstrap`, no gateway restart, no GUI-op required.
2. **Media-caps before/after (P6-122) — measured: after-window NOT open.** All canary entries are same-day (07-05); pa still 229K med / 20% trunc because the pre-caps browser turn (`96869d34`) is still in-window and no post-caps browser/PDF turn has run. Honest "insufficient data" — §8 thresholds kept, no recalibration off nonexistent data. Caps confirmed live (Stage 0). Carry: read the canary over coming days.
3. **Doctor orphan-detection CORRECTED (P6-122) + safe manifest staged.** Naive filename match flags **live-session `.trajectory`/`.checkpoint` sidecars** as orphans (e.g. `831bded7…` = live pa:main) — unsafe. Correct = by session-ID prefix excluding live ids: **41 files / 206.5 MB / 35 old ids**, 8 live sidecars excluded. Manifest read-only at `state/doctor-orphan-manifest-6c24.txt` with a guarded review-then-`rm`. `rm` deny-listed → **Peter runs**.

## Judgement calls
- **Kept strictly to the interim mandate.** The two high-value moves (aggressive frozen-core slice, dreaming-on) are quality-impacting substrate changes Peter explicitly ring-fenced to Andreas — **not attempted**, even though reversible. The reset net is session-management (not a memory-content change) → the safe interim work the opener greenlit.
- **Human-chat guard is the core safety design.** An auto-reset triggers a gateway restart; doing that under Peter mid-conversation is the one irreversible harm, so section (D) DEFERS while any human chat is active (<10m). The bloat waits one cycle; a live turn can't.
- **Did not fire the real reset+restart to "prove" it.** That restarts the live gateway (interrupts sessions) — irreversible-class, surfaced-not-auto. It is the same mechanism as the proven-live 06:55 job + the separately-verified `--agent` flag; DRYRUN+fixtures cover the novel logic.
- **Declined a live `browser navigate`** for media-caps evidence — spins up the shared browser (pro was active) for marginal gain over confirmed-live config.
- **Corrected my own wrong reasoning in-flight:** the "piped `while` runs in a subshell in zsh" premise behind the here-string change is false (zsh runs the last pipeline stage in the current shell); fixed the misleading code comment rather than leave misinformation.
- **Caught the false-orphan trap before handing Peter a list** — a naive manifest would have deleted live-session sidecars.

## SHA re-anchor (post-session)
- **openclaw.json** `eb08cfa7d7b6`/10654B — **UNCHANGED** (no config edit; A-7 live==last-good, 600/staff). Pin 2026.4.22 (00bd2cf), sandboxes `capable-2026-06`. *(A-7 byte-hash is Bash-deny-listed to Claude Code — integrity verified via the applied-lever values in Stage 0, not a direct file hash; the F21 backstop was not weakened.)*
- **`scripts/session-freshstart.sh`** `fc75bc0d5133`/6009B → **`55020ef7acd6`/6637B**.
- **`scripts/heartbeat-watchdog.sh`** `2e93b6c40910`/14313B → **`183896a33dd2`/20438B**.
- **`_session-docs/PHASE-6-DEVIATIONS-LOG.md`** → **`ab1a1653b2c2`/361144B** (P6-121, P6-122).
- **New (read-only artefact):** `state/doctor-orphan-manifest-6c24.txt` (41 files/206.5 MB, for Peter).
- **Memory index (unchanged):** pa 35/35·125, pro 31/31·36, `Dirty: no`, Dreaming off, recall 12 entries · **0 promoted**.
- **Deviations log:** P6-1…122 **gap-free, no dangling refs** (historical 21/22 dupes left per opener).

## Carry-chain audit ([P6-101])
- **Mechanical:** P6-1…122 gap-free; no referenced-but-undefined codes; only the pre-existing 21/22 duplicate headings (historical, left).
- **Carry diff vs 6c.23 closure "Carried → 6c.24":** ① aggressive core restructure + billed verify turn = **⛔ Andreas-only, consciously NOT attempted** (opener flag honoured); ② media-caps before/after = **measured — after-window not open**, carried with reason; ③ **intra-day `:main`-bloat durable fix = DONE (P6-121)** — the session's lead; ④ dreaming-on = **⛔ Andreas-only, untouched** (`Dreaming: off`, no DREAMS.md); ⑤ Porsche re-deliver = **parked** (Peter retracted the finding 6c.23; not re-surfaced); ⑥ doctor hygiene = **advanced** — orphan-detection corrected + safe manifest staged for Peter (P6-122); ⑦ Stage-2 (payments real-charge F26, reply-watcher, generate-mode cred, Battery) = **carried** (billed/2FA/deny-listed — none interim-autonomous). Nothing silently dropped.

## Carried → 6c.25 (in the opener)
- **Observe the reset net in the wild** — once a real intra-day breach occurs, confirm `run_reset` fired, cooled down, and notified (read `logs/heartbeat-watchdog.log` + the `reset-<agent>.state` sentinel). First true production F26.
- **Media-caps before/after** — the after-window opens as browser/PDF turns run; recalibrate §8 off the real "after".
- **Doctor hygiene** — Peter authorises/runs `state/doctor-orphan-manifest-6c24.txt` (41 files/206 MB) + dispositions the extra `agents/` dirs.
- **⛔ Andreas-only (still ring-fenced, do NOT attempt in interim):** aggressive frozen-core slice (≤30K, verb-tables → retrieval-only) + its billed fresh-session verify turn (recall=0 gate); dreaming-on + first DREAMS.md review → then the §8 week-clock.
- **Stage-2:** payments real-charge F26 (Peter 2FA), reply-watcher (Gmail poller → `--deliver`), generate-mode cred store ([P6-115]), Battery re-run (billed, ~$5, needs Peter's OK).

## Acceptance gate
Stays **OPEN**. 6c.24 closed the intra-day-bloat bounding gap (the last reversible session-management item); the remaining gate signals are all Andreas-owned or billed: organic recall > 0 (needs the aggressive slice + a billed turn), the media-caps before/after falling, and dreaming-on. The week-clock starts only once those land.
