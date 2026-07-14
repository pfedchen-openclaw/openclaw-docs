# SESSION 6c.33 — CLOSURE: 6d Andreas PAIRED + F26 PASS (the lead delivered); Marie screenshot F26 = FAIL, pinpointed

_The billed/irreversible pairing cluster. Andreas (`cos`) is now a live, F26-verified control-plane agent. The bundled Marie screenshot-delivery F26 failed and is now sharply diagnosed. Everything reversible ran under the autonomy posture; every irreversible/billed step was Peter-gated + surfaced. **openclaw.json `3267edd8b6aa`→`24715f4045bd`/10748B (A-7 clean).** Deviations: **P6-145, P6-146**._

## Done
- **Stage 0 GREEN + reconciled.** A-7 clean; carry-chain audit PASS (P6-1…144 gap-free). Opener's Stage-0 foundation shas were the P6-140 *build* values; current files are the post-[P6-142/144] evolutions and all reconcile. **Anchor fix:** [P6-144] anchored `workspace-cos/AGENTS.md` garbled ("`3c…`"); real = **`09a1aeb76564`/17176B** (verified = the intended per-action cost-model edit). Recorded in P6-145 (append-only; line 1320 untouched).
- **★★ Andreas activated + paired + F26 PASS (the lead).**
  - `cos` block: added `name:"Andreas"` + 6h Sonnet heartbeat; **A2A/gateway OFF** (`alsoAllow=[cron]`). Applied via guarded `apply-6c33-cos-activate.sh` (F17). Gateway restart (GUI/D20) → `cos (Andreas)` registered, healthz 200.
  - **Finding:** `@andreas_pf_bot` was **already paired + routing since 2026-07-08** (opener's "new BotFather pairing" was moot); only the inbound **sender-approval** was pending — Peter's first msg logged `matchKey:none` (no turn, £0). Resolved via `openclaw pairing approve … 872VFULP`.
  - **F26 PASS:** cos flipped opus→sonnet (hot-reload), one live Telegram→gateway turn (13:27), reverted opus. Andreas booted his brief, in-voice (no DM sign-off), propose-not-execute (drafts→routes to Charlotte→human gate, never sends). Real-runtime verified (not `--local`). Revert byte-clean.
- **★ A2A [P6-142] design gates resolved (recommendation):** (a) hub-synchronous topology; (b) host-authoritative hop/path (ledger-reconstructed); (c) L3→real-$ via [P6-144] cost model, daily counters already date-keyed. Ratify at 6d A2A-wiring; blocks *live* only. `README §Open` left as-is pending ratification.
- **Marie screenshot F26 = FAIL, pinpointed (P6-146).** PNG created (`media/browser/a048acc0…`, 13:43) but delivered as a `MEDIA:~/…png` **tilde text directive** → `sendMessage` not `sendPhoto` → stripped, no image; Marie falsely claimed success. Confirms + sharpens [P6-144]④ (agent-turn attachment path is the bug; mechanism = tilde text directive vs structured absolute attachment). Fix carried (not rushed on a payments-critical path).

## State (SHA-anchored)
- **openclaw.json** `24715f4045bd`/10748B, 600/staff, live==last-good (A-7 clean). Only persistent change: the `cos` activation (+259B). Both Sonnet flips (cos, pa) reverted byte-clean.
- **Agents:** `cos`=Andreas/opus-4-7/6h-heartbeat, paired, A2A OFF. `pa`=Marie/opus-4-7 (reverted). Gateway healthz 200 (port 18789), 5 telegram providers clean.
- **New helpers (config repo):** `scripts/apply-6c33-cos-activate.sh`, `apply-6c33-cos-model.sh`, `apply-6c33-agent-model.sh` (all zsh -n clean, fixture-proven, guarded Peter-run `!`).
- **Unchanged/foundation:** `workspace-cos/` briefs, `a2a-watch.py f3460ae17105`, `carry-chain-audit.py 375f8e9dc1e9`, `shared/a2a/` (HALT present=fail-closed, ledger empty). A2A remains INERT.

## Carried → 6c.34
1. **★ Marie screenshot-delivery FIX + F26 [P6-146]** — payments/booking-critical. Make the agent screenshot-reply a structured absolute-path attachment (not `MEDIA:~/…` tilde text); check tilde origin (verb/ROP/helper), config-vs-dist, and the [P6-120] media-caps regression hypothesis; then real-Marie F26-verify. Candidate for the browser last-mile [P6-130]/Andreas bucket.
2. **★ Andreas Stage-2 object-level remit** (each F26-verified before the next): the per-action cost model [P6-144]/[P6-85] (flagship); the 08:00 digest cron — **use the host-launchd pattern** (`ai.openclaw.cos-briefing.plist`), the `cron` agent-tool is inert (`cron.enable` unset, P6-145); SR-1 doc-currency (first task: v7 PART 10 §SR-1 `openclaw-config`→`openclaw-docs` drift, [P6-141]).
3. **A2A live (6d wiring)** — code the resolved gates (hub-sync constructor + ledger-based hop/path), bootstrap `a2a-watch.plist` in `A2A_MODE=dryrun`, prove over real traffic, then flip live + remove HALT + grant `sessions_send`/hard-gated `sessions_spawn`.
4. **Andreas avatar** — first male portrait (§e spec: charcoal/navy suit, red lobster brooch, gold "A", vs Charlotte exemplar); BotFather `/setuserpic` + `/setname` + `/setdescription`, then `set-identity`. Cosmetic; non-blocking.
5. **cron subsystem decision** (P6-145) — host-launchd (lean) vs enable `cron.enable`; decide at the 08:00-cron build.
6. **Optional reversible (Peter-run, non-blocking):** reply-watcher NOTIFY→DRYRUN plist reload ([P6-144], if not yet done); `CHAT_RESET_TOKENS`→~70000; MEMORY.md RATIONALE-rotation ([P6-116]); Charlotte/Marie avatars.

## Discipline
Cost: one billed Sonnet turn (Andreas F26) + one billed Sonnet turn (Marie F26) — both authorised, Sonnet-only, no loops/Opus. Everything else host-side inspection/CLI (£0). No secrets read (structural `config get` + `shasum` only). Irreversible/billed steps all Peter-gated + surfaced. openclaw.json edits F17/SHA-anchored, Peter-applied via `!`.
