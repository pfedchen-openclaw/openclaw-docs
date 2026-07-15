# SESSION 6c.36 — **A2A live groundwork (Stage 3)**: code the [P6-145] gates → dryrun `a2a-watch`; + verify the CoS morning flip (activated 6c.35) on its first live morning

_6c.35 resolved the carried digest de-dupe — Peter chose **flip to CoS-consolidated** (retire Marie's direct send) and the operator **activated it live** in the GUI — the CoS briefing is the single 08:00 message from 16 Jul; corrected the handoff off the **unreachable `shared/` path** to in-workspace ([P6-150]). Also finished **MEMORY.md rotation** ([P6-116], 6467→5556, under cap) and **SR-1 doc-currency** ([P6-141], v7 + derivation). A2A was **deferred by Peter** to this focused run. openclaw.json UNCHANGED (`24715f4045bd`/10748B, A-7 clean)._

## Stage 0 — Ledger (no mutating work until GREEN)
- **A-7:** live==last-good==**`24715f4045bd`/10748B**, 600/staff. Pin 2026.4.22. `cos`=Andreas/opus-4-7/A2A-OFF, `pa`=Marie/opus-4-7, `pro`=Charlotte/sonnet-4-6.
- **★ Dist patch persists:** `reply-media-paths.runtime-DegAVXTm.js` == **`6dfbfe762078`**/6847B (re-run `apply-6c34-screenshot-media-fix.sh` + restart if an `openclaw` reinstall reverted it).
- **Deviations currency:** P6-1..150 gap-free — re-run `scripts/carry-chain-audit.py` at open AND close.
- **Context/GUI ([P6-117]/D20):** confirm `SSH_CONNECTION` empty + `gui/502` before any restart/billed turn. Billed test = Sonnet, one short turn, re-confirm first ([[no-unauthorized-test-spend]]).
- **★ Morning flip is LIVE (activated 6c.35).** The interlock was disarmed (`shared/cos/BRIEF-SEND-HOLD` → `.retired-6c35`) and Marie's direct send retired; the CoS briefing is the single 08:00 message from 16 Jul. **Verify the first live morning:** one clean consolidated message arrived (no double/no gap) and Marie actually wrote `workspace-pa/memory/digest-2026-07-16.md` (else memory-log fallback was used — acceptable, note it). Revert if wrong: `mv shared/cos/BRIEF-SEND-HOLD.retired-6c35 shared/cos/BRIEF-SEND-HOLD` + restore the PA-PLAYBOOK sentence.

## Stage 1 — A2A live groundwork (the deferred build; each gate coded + tested before wiring)
Current: `a2a-watch.py` (**`f3460ae17105`**/433L) is INERT (HALT present, `ledger.jsonl` empty) and validates **agent-supplied** hop/path. Code the resolved [P6-145] gates — **their design is a recommendation; ratify with Peter at the top of the build**:
1. **(a) Hub-synchronous envelope constructor** — the originating human turn mints `conversation_id`; a reply returns **inline within the dispatch turn** (not a new envelope); the **host** increments `hop` / appends `path`. So the L1 cycle-check + per-pair budget guard only genuine *new* dispatches (no reply-exemption hack).
2. **(b) Host-authoritative hop/path** — the watcher **reconstructs** `path`/`hop` from `ledger.jsonl` per `conversation_id`; agent-asserted values are untrusted hints (closes the TTL-under-report spoof). Unit-test both against synthetic ledgers.
3. **THEN** dryrun-bootstrap the staged `ai.openclaw.a2a-watch.plist` in **`A2A_MODE=dryrun`** (GUI/D20, Peter-run launchctl) → verify `ledger.jsonl` over real traffic. Only later flip `A2A_MODE=live` + remove `shared/a2a/HALT` + grant `sessions_send` (hard-gated `sessions_spawn`). Kill lever: `touch shared/a2a/HALT`. **Do NOT flip live this session** unless the dryrun is clean AND Peter authorises.

## Stage 2 — carried reversibles (only if A2A lands with headroom)
- **Andreas avatar** (first male portrait, §e spec) — optional reversible.
- **`CHAT_RESET_TOKENS`→~70000**; reply-watcher plist reload if pending.

## Housekeeping / carried
- **Cost-model** absolute reconciliation awaits Peter's **Admin API key**; coverage fix (read archived/gzipped trajectories) is **Andreas-owned backlog** — not an interim pickup ([P6-148]).
- **Charlotte handoff:** currently flows to the briefing via memory-log fallback; a dedicated `workspace-pro/memory/digest-<date>.md` is a one-line brief nudge if Peter wants her curated content verbatim.
- **Travel agent** next after Andreas (~early Aug, [P6-147]); screenshot delivery (its hard dependency) is fixed.

## Safety rails
Reversible reads/host-authoring/`git commit` run without prompting. **Surface irreversible/outward:** openclaw.json edits (F17 staged, Peter via `!`) + gateway restart (GUI/D20); any billed turn (Sonnet-only, re-confirm); **enabling A2A live**; real external sends; the morning-flip activation. `openclaw.json` jq/mv/cp + raw `launchctl` + `op` mutations + `rm` + `docker exec` DENY-listed → Peter via `!`. **Secrets [F21/F51]:** never Read openclaw.json / agent-creds/** — structural `config get`/`jq`-on-keys + `shasum` only; reference creds by path, never value.

## Carry-forward — status
1. **✔ Acceptance gate (6c.30); ✔ v7 CAPSTONE (6c.31); ✔ Andreas foundation (6c.32); ✔ PAIRED+F26 (6c.33); ✔ Marie screenshot fix (6c.34, [P6-146]); ✔ Andreas Stage-2 objects (6c.34); ✔ digest de-dupe → CoS-flip built + MEMORY rotation + SR-1 doc-currency (6c.35, [P6-150]/[P6-116]/[P6-141]).**
2. **→ THIS SESSION:** A2A live groundwork (code [P6-145] gates → dryrun) + verify the morning flip's first live 08:00 (activated 6c.35). (A2A was deferred from 6c.35 by Peter for a focused run.)
