# SESSION 6c.38 — Resume the Andreas/A2A track: Stage 2 (agent-side envelope emitter) → verify the 6c.37 Marie mitigations bit in production

_6c.37 shipped the bounded Marie reliability mitigation batch (Stage-1, structural Andreas-independent harm-reducers): memory-flush dedup root-caused + fixed (92 dup sections / ~180KB removed, cadence-wired, delta-only root staged) [P6-154]; reset-net 130K→90K + graceful heads-up [P6-155]; status-SLA open-loops (Peter-ratified escalate mode, built inert) [P6-156]; micro-skills seeded [P6-157]; draft-delete dropped per Peter, never-delete kept [P6-158]. £0, openclaw.json UNCHANGED (`24715f4045bd`/10748B, A-7 clean). The four durable roots stay Andreas-owned._

## Stage 0 — Ledger (no mutating work until GREEN)
- **A-7:** live==last-good==**`24715f4045bd`/10748B**, 600/staff. Pin 2026.4.22 (`00bd2cf`). `cos`=Andreas/opus-4-7/**A2A-OFF**, `pa`=Marie/opus-4-7, `pro`=Charlotte/sonnet-4-6.
- **★ Dist patch persists:** `reply-media-paths.runtime-DegAVXTm.js`==**`6dfbfe762078`**/6847B (the `BsZ-hV3v` 193B chunk is the known orphan — don't re-panic). Re-apply `apply-6c34-screenshot-media-fix.sh` + restart only if `DegAVXTm` itself reverts.
- **Deviations currency:** P6-1..158 gap-free — re-run `scripts/carry-chain-audit.py` at open AND close.
- **Context/GUI ([P6-117]/D20):** confirm `SSH_CONNECTION` empty + `gui/502` before any restart/billed turn. Billed test = Sonnet, one short turn, re-confirm first ([[no-unauthorized-test-spend]]).
- **★ A2A resting posture INERT.** `shared/a2a/HALT` PRESENT; plist staged-not-loaded; `a2a-watch.py`==**`96aa0247e52a`**; `sessions_send`/`sessions_spawn` OFF.
- **6c.37 mitigations — check they took:** `scripts/memory-dedup.py`==**`4f5494c5c27d`**, `open-loops-watch.sh`==**`84715da28689`**, `chat-bloat-reset.sh`==**`3a7bd2f71c15`** (90K), `memory-health.py`==**`969c99ea31e8`** (autoheal). `open-loops.json`==`657c9cf769f5`. `AGENTS.md`==`2721b4a909ff`.

## Stage 1 — Verify the 6c.37 mitigations bit in production (inspection-first, £0)
Fast read-only confirmations before new build (each is a real-runtime check, not inspection-of-code):
1. **Dedup holding:** `python3 scripts/memory-dedup.py --agent pa --day $(date +%F)` → expect **0 removable** (autoheal + intra-day hook keeping it clean). `grep dup logs/... ` / `memory-health.py` dry-run shows `dup×1`.
2. **Reset at 90K + heads-up:** `tail logs/chat-bloat-reset.log` — did a real reset fire below the old 130K, and did the "🧹 tidying my notes" heads-up send (if a bloated+idle window occurred)?
3. **Open-loops watcher (if Peter bootstrapped it):** `tail logs/open-loops-watch.log` — dryrun/escalate firing cleanly over Marie's populated `open-loops.json`? Is Marie actually registering loops (brief §62 landed)? If not bootstrapped yet, that's the [P6-156] handoff — offer the 3 launchctl lines.
4. **Delta-only flush root:** if Peter ran `apply-6c37-memory-flush-prompt.sh apply` + a restart, confirm a post-restart flush leaves the day file single-copy.
Anything not-yet-applied → surface the Peter-gated handoff (Closure §Peter-gated), don't silently carry.

## Stage 2 — A2A agent-side envelope emitter (resume the Andreas/A2A track — THE main build)
The **host** gates (a)+(b) are done + proven ([P6-152]); no agent emits an envelope yet.
1. **Emitter convention (AGENTS.md/TOOLS.md).** Hub (Andreas) mints `conversation_id` on the originating **human** turn; a spoke needing a specialist drops a JSON envelope into `workspace-<id>/.a2a-outbox/pending/` (plain file-write — **no** openclaw.json grant); it **reads the reply from `results/`** and **never re-sends** (gate (a): replies are inline). Give Andreas the hub emitter + Marie/Charlotte the spoke read-path. Derive from the CURRENT COS/PA/PRO playbooks ([P6-26]).
2. **Dryrun over REAL traffic.** Bootstrap the plist `A2A_MODE=dryrun` (GUI/D20, Peter-run), drive one real hub→spoke ask (single cheap Sonnet turn if authorised) → verify `ledger.jsonl` shows the host-authoritative envelope over genuine agent output.
3. **THEN live (Peter-gated, GUI, openclaw.json change + restart — SURFACE it).** Flip `A2A_MODE=live` + remove `shared/a2a/HALT` + grant `sessions_send` (hard-gate `sessions_spawn`). Kill lever: `touch shared/a2a/HALT`. **Do NOT flip live** unless the real-traffic dryrun is clean AND Peter authorises.

## Stage 3 / carried — the Andreas track proper (the deep Marie cures live here)
- **★ Andreas-owned durable Marie roots ([P6-153]):** aggressive frozen-core brief-slice (§A.4 HEADLINE — kills bloat/latency/stick; today Marie was 222K med / 63% trunc); engine-level live-chat keep-alive + bounded worker-session isolation; production retrieval recall activation ([P6-118]/[P6-137]) — **also the enabler for [P6-157] micro-skills auto-recall**; hard pulse send-gate ([P6-128]) + per-task model routing ([P6-28]/[P6-144]).
- **`@fedchenkov` deliverability optimisation** (Note 3) → Andreas stewardship (DNS = propose-only).
- **In-place `draft-update` verb** ([P6-158]) — optional Peter-gated apply if the Drafts-folder clutter persists (guarded-file edit + container recreate).
- **Restaurant/place screenshot preferences store** → deferred, event-gated on Travel (~early Aug, [P6-147]); needs the retrieval layer.
- **Andreas avatar** (first male portrait) — optional reversible. **Cost-model** absolute reconciliation awaits Peter's Admin API key ([P6-148]). **Charlotte handoff** — dedicated `digest-<date>.md` if Peter wants her content verbatim.

## Safety rails
Reversible reads/host-authoring/`git commit` run without prompting. **Surface irreversible/outward:** openclaw.json edits (F17 staged, Peter via `!`) + gateway restart (GUI/D20); any billed turn (Sonnet-only, re-confirm); **enabling A2A live**; real external sends; **flipping the open-loops watcher to escalate** (billed Opus pokes — after a clean dryrun shakedown). `openclaw.json` jq/mv/cp + raw `launchctl` + `op` mutations + `rm` + `docker exec` DENY-listed → Peter via `!`. **Secrets [F21/F51]:** never Read openclaw.json / `agent-creds/**` — structural `config get`/`jq`-on-keys + `shasum`/`docker inspect` only. Keep config checks on their own Bash line (6c.36 guard note).

## Carry-forward — status
1. **✔ through 6c.36** (acceptance gate, v7 capstone, Andreas foundation+PAIRED, screenshot fix, morning flip verified, A2A host gates, Marie deep-audit). **✔ 6c.37:** Marie mitigation batch (Stage-1) shipped [P6-154..158].
2. **→ THIS SESSION:** verify the 6c.37 mitigations in production (Stage 1) → **resume the A2A emitter (Stage 2, the main build)** → Andreas roots (Stage 3). The deep Marie cures live in the Andreas track, not in more mitigations.
