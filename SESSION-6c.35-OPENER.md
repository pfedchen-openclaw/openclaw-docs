# SESSION 6c.35 — **Andreas Stage-2 finish** (install briefing + reconcile cost model + digest-source + SR-1), then **A2A live (Stage 3)** groundwork

_6c.34 delivered the lead — **Marie screenshots now reach Peter** ([P6-146], F26 PASS: 2-edit design-faithful dist patch to the sandbox reply-media normalizer + brief fix) — and shipped Andreas's two Stage-2 objects: the **per-action cost model** (`cost-breakdown.py`, cache≈90%/naive-meter-10-13×-under, reconciliation wired) and the **08:00 CoS briefing v1** (`cos-briefing.py` + staged plist, DRYRUN-verified). openclaw.json UNCHANGED (`24715f4045bd`/10748B, A-7 clean). Deviations P6-146(resolved)/P6-148._

## Stage 0 — Ledger (no mutating work until GREEN)
- **A-7:** live==last-good==**`24715f4045bd`/10748B**, 600/staff. Pin 2026.4.22. `cos`=Andreas/opus-4-7/6h/A2A-OFF, `pa`=Marie/opus-4-7, `pro`=Charlotte/sonnet-4-6.
- **★ Dist patch to verify PERSISTS:** `~/.npm-global/lib/node_modules/openclaw/dist/reply-media-paths.runtime-DegAVXTm.js` == **`6dfbfe762078`**/6847B (backup `.orig-6c34`=`cf7231b259ef`). If an `openclaw` reinstall reverted it → re-run `scripts/apply-6c34-screenshot-media-fix.sh` + gateway restart. Marie screenshot delivery depends on it.
- **Deviations currency:** P6-1..148 gap-free — re-run `scripts/carry-chain-audit.py` at open AND close.
- **Context/GUI ([P6-117]):** confirm `SSH_CONNECTION` empty + `gui/502` before any restart/billed turn. Billed test = Sonnet, one short turn, re-confirm first ([[no-unauthorized-test-spend]]).

## Stage 1 — finish Andreas Stage-2 (mostly Peter-gated installs + a reconcile; each verified)
1. **Install the 08:00 briefing** (Peter/GUI-D20): copy `scripts/ai.openclaw.cos-briefing.plist.staged` → `~/Library/LaunchAgents/ai.openclaw.cos-briefing.plist`, `launchctl bootstrap gui/502 …`. **First a £0 smoke test:** `python3 ~/.openclaw/scripts/cos-briefing.py --send --target 253509519` → Peter confirms it renders well in Telegram. (DRYRUN already verified assembly; this confirms delivery + formatting.)
2. **Cost-model reconciliation — NOT this session (Peter 6c.34; Andreas owns the decision).** Admin API confirmed **unavailable** (no Admin Keys on the account). The model **undercounts absolute-$ ~4×** vs the agents-only Console total (coverage: misses rotated/archived trajectories) — logged as an **Andreas-owned potential improvement** in `PRE-COS-BACKLOG.md §addendum(2)`, deferred. Do NOT pick it up next session. Relative attribution + optimisation (cache ≈90%) are already trustworthy and need no calibration.
3. **Digest-source reconciliation** ([P6-148]) — decide the fleet digest location (`shared/daily-digests` vs the real `workspace-*/memory/<date>.md`) + **de-dupe the CoS briefing vs Marie's existing 8am digest** (avoid two morning messages). Update `cos-briefing.py` + the agents' briefs accordingly.
4. **SR-1 doc-currency** ([P6-141]) — Andreas's first steward task: reconcile v7 PART 10 §SR-1 `openclaw-config`→`openclaw-docs`.

## Stage 2 — A2A live (6d wiring; Stage 3, only when Stage-1 stable)
FIRST code the resolved [P6-142] gates ([P6-145]): (a) hub-synchronous envelope constructor (host mints `hop`/appends `path`); (b) host-authoritative hop/path from `ledger.jsonl` per `conversation_id`. THEN bootstrap the staged `a2a-watch.plist` in **`A2A_MODE=dryrun`** (GUI/D20), verify `ledger.jsonl` over real traffic. Only then flip `A2A_MODE=live` + remove `shared/a2a/HALT` + grant `sessions_send` (+ hard-gated `sessions_spawn`). Kill lever: `touch shared/a2a/HALT`.

## Housekeeping / carried
- **★ [P6-116] MEMORY.md RATIONALE-rotation — NOW URGENT.** `workspace-pa/MEMORY.md` = 6395 chars, over the 5909 self-truncate cap → oldest tail rules silently lost. Rotate coldest directives to `reference/MEMORY-RATIONALE.md` (one-in-one-out).
- **Travel agent** next after Andreas (~early Aug, [P6-147]) — its one hard dependency (screenshot delivery) is now FIXED; it's a scaffold-registered roster agent → block-completion + pairing + F26 when we get there.
- **Optional reversible (Peter):** Andreas avatar (first male portrait, §e spec); `CHAT_RESET_TOKENS`→~70000; reply-watcher plist reload if pending.

## Safety rails
Reversible reads/host-authoring/`git commit` run without prompting. **Surface irreversible/outward:** openclaw.json edits (F17 staged, Peter via `!`) + gateway restart (GUI/D20); any billed turn (Sonnet-only, re-confirm); enabling A2A; real external sends. `openclaw.json` jq/mv/cp + raw `launchctl` + `op` mutations + `rm` + `docker exec` DENY-listed → Peter via `!`. **Secrets [F21/F51]:** never Read openclaw.json / agent-creds/** — structural `config get`/`jq`-on-keys + `shasum` only; reference creds by path, never value.

## Carry-forward — status
1. **✔ Acceptance gate (6c.30); ✔ v7 CAPSTONE (6c.31); ✔ Andreas foundation (6c.32); ✔ Andreas PAIRED+F26 (6c.33); ✔ Marie screenshot fix + F26 (6c.34, [P6-146]); ✔ Andreas Stage-2 objects built (6c.34, [P6-148]).**
2. **→ THIS SESSION:** install/verify briefing + reconcile cost model + digest-source + SR-1 → A2A live groundwork.
