# SESSION 6c.37 — **A2A next increment: the agent-side envelope emitter** (wire Andreas + spokes to emit → dryrun over real traffic), toward the Peter-gated live flip

_6c.36 landed the deferred A2A **host** groundwork: the [P6-145] gates were **ratified by Peter**, then **coded + unit-tested 20/20 + production dryrun-bootstrapped** through real launchd — gate (a) hub-synchronous inline reply (no reverse envelopes) + gate (b) host-authoritative hop/path from `ledger.jsonl` (closes the TTL-under-report spoof). Both loop/cost spoofs demonstrably caught on the real mailbox, £0, A2A left INERT ([P6-152]). Also verified the 6c.35 morning flip's **first live 08:00** fired clean — one consolidated message, Marie's in-workspace handoff worked on first use ([P6-150] confirmed). openclaw.json UNCHANGED (`24715f4045bd`/10748B, A-7 clean)._

## Stage 0 — Ledger (no mutating work until GREEN)
- **A-7:** live==last-good==**`24715f4045bd`/10748B**, 600/staff. Pin 2026.4.22 (`00bd2cf`). `cos`=Andreas/opus-4-7/**A2A-OFF**, `pa`=Marie/opus-4-7, `pro`=Charlotte/sonnet-4-6.
- **★ Dist patch persists:** `reply-media-paths.runtime-DegAVXTm.js` == **`6dfbfe762078`**/6847B. **NB — don't re-panic:** a second `reply-media-paths.runtime-BsZ-hV3v.js` (193B, Apr 24) is an **unrelated orphan chunk**, not a reinstall; the patched chunk is `DegAVXTm`. Re-run `apply-6c34-screenshot-media-fix.sh` + restart only if `DegAVXTm` itself reverts.
- **Deviations currency:** P6-1..152 gap-free — re-run `scripts/carry-chain-audit.py` at open AND close.
- **Context/GUI ([P6-117]/D20):** confirm `SSH_CONNECTION` empty + `gui/502` before any restart/billed turn. Billed test = Sonnet, one short turn, re-confirm first ([[no-unauthorized-test-spend]]).
- **★ A2A resting posture is INERT (verified 6c.36).** `shared/a2a/HALT` PRESENT (fail-closed); plist **staged-not-loaded**; `a2a-watch.py` == **`96aa0247e52a`**/512L (gates (a)+(b) live in code); `ledger.jsonl` holds 3 `dryrun-smoke-6c36` records (mode=dryrun evidence — ignore for live). `sessions_send`/`sessions_spawn` OFF in openclaw.json.

## Stage 1 — A2A agent-side envelope emitter (the remaining wiring before live)
The **host** gates are done + proven. What's missing: **no agent emits an envelope yet.** Ratify the emitter convention with Peter, then wire it (reversible doc/helper work), dryrun over real agent traffic, and only then do the Peter-gated live flip.
1. **Emitter convention (AGENTS.md/TOOLS.md).** The hub (Andreas) mints `conversation_id` on the originating **human** turn; an agent that needs a specialist drops a JSON envelope into `workspace-<id>/.a2a-outbox/pending/` (a plain file-write — needs **no** openclaw.json grant); it **reads the reply from `results/`** and **never re-sends** (gate (a): replies are inline, not envelopes). Give Andreas the hub emitter + Marie/Charlotte the spoke read-path. Derive from the CURRENT COS/PA/PRO playbooks (P6-26), not a template.
2. **Dryrun over REAL traffic.** With the emitter wired, bootstrap the plist in `A2A_MODE=dryrun` (GUI/D20, Peter-run) and drive **one real** hub→spoke ask (a single cheap agent turn if authorised — Sonnet, [[no-unauthorized-test-spend]]) → verify `ledger.jsonl` shows the host-authoritative envelope over genuine agent output, not seeded synthetic.
3. **THEN live (Peter-gated, GUI, openclaw.json change + restart — SURFACE it).** Flip `A2A_MODE=live` + remove `shared/a2a/HALT` + grant `sessions_send` (hard-gate `sessions_spawn`). Kill lever unchanged: `touch shared/a2a/HALT`. **Do NOT flip live** unless the real-traffic dryrun is clean AND Peter authorises.

## Stage 2 — carried reversibles (only with headroom)
- **Andreas avatar** (first male portrait, §e spec) — optional reversible.
- **`CHAT_RESET_TOKENS`→~70000**; reply-watcher plist reload if pending.
- **Charlotte handoff** — a dedicated `workspace-pro/memory/digest-<date>.md` if Peter wants her curated content in the briefing verbatim (currently memory-log fallback).

## Housekeeping / carried
- **Cost-model** absolute reconciliation awaits Peter's **Admin API key**; coverage fix (archived/gzipped trajectories) is **Andreas-owned backlog** ([P6-148]).
- **Travel agent** next after Andreas (~early Aug, [P6-147]); screenshot delivery (hard dependency) fixed.

## Safety rails
Reversible reads/host-authoring/`git commit` run without prompting. **Surface irreversible/outward:** openclaw.json edits (F17 staged, Peter via `!`) + gateway restart (GUI/D20); any billed turn (Sonnet-only, re-confirm); **enabling A2A live** (the `sessions_send` grant + HALT removal); real external sends. `openclaw.json` jq/mv/cp + raw `launchctl` + `op` mutations + `rm` + `docker exec` DENY-listed → Peter via `!` (apply-scripts that call these are Peter-run). **Secrets [F21/F51]:** never Read openclaw.json / agent-creds/** — structural `config get`/`jq`-on-keys + `shasum` only; reference creds by path, never value. (6c.36 note: a Bash line that merely *names* `openclaw.json` alongside other ops can trip the guard — keep config checks on their own line.)

## Carry-forward — status
1. **✔ Acceptance gate (6c.30); ✔ v7 CAPSTONE (6c.31); ✔ Andreas foundation (6c.32); ✔ PAIRED+F26 (6c.33); ✔ Marie screenshot fix (6c.34); ✔ digest→CoS-flip + MEMORY rotation + SR-1 currency (6c.35); ✔ CoS morning flip VERIFIED live + A2A host gates (a)+(b) CODED/tested/dryrun-bootstrapped (6c.36, [P6-150]/[P6-152]).**
2. **→ THIS SESSION:** A2A **agent-side envelope emitter** (wire hub+spokes → dryrun over real traffic) → then the Peter-gated live flip. The host gates are proven; this is the remaining wiring.
