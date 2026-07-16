# SESSION 6c.36 — CLOSURE: A2A gates (a)+(b) CODED + tested + production dryrun-bootstrapped ([P6-152]); morning flip's first live 08:00 VERIFIED clean ([P6-150])

_The deferred A2A live groundwork landed. The [P6-145] design was **ratified by Peter at the top of the build**, then both gates were coded into `a2a-watch.py`, **unit-tested 20/20**, and **dryrun-bootstrapped through the real launchd path** over the production mailbox — both loop/cost spoofs (reply-as-envelope + hop under-report) demonstrably caught, £0 throughout, A2A left INERT. Separately, the carried LEAD — the 6c.35 morning flip's **first live consolidated 08:00** — fired **clean** (one message, no double/gap; Marie's in-workspace handoff worked on first use, confirming the [P6-150] reachable-path fix). Everything reversible ran under the autonomy posture; the two decisions surfaced (design ratification; how far to take the bootstrap) were Peter's. **£0 — no billed agent turns. openclaw.json UNTOUCHED (`24715f4045bd`/10748B, A-7 clean).** Deviations: **P6-152 new; P6-150 first-live-verify appended.**_

## Done
- **Stage 0 GREEN.** A-7 clean (live==last-good==`24715f4045bd`/10748B/600/staff); carry-chain audit PASS (P6-1..152 gap-free, append-only); GUI/D20 context (SSH empty, `gui/502` reachable); dist patch persists (`reply-media-paths.runtime-DegAVXTm.js` = `6dfbfe762078`/6847B — the `BsZ-hV3v` 193B chunk is an unrelated orphan, **not** a reinstall; version still `2026.4.22 (00bd2cf)`). Caught up P6-151 (post-6c.35 Andreas cost-lane finding — matches memory).
- **★ CARRIED LEAD — morning flip's first live 08:00 VERIFIED clean ([P6-150] append).** `cos-briefing` launchd `runs=1`/`exit 0`/empty err; out-log `briefing sent (2026-07-16) via cos -> 253509519` = one consolidated message, **no double, no gap**. Marie wrote the real in-workspace handoff `workspace-pa/memory/digest-2026-07-16.md` (3056B, substantive — Aviva/Pasha 10:00 clash + National Windscreens cancellation threat), i.e. **NOT** the memory-log fallback → the [P6-150] reachable-path correction is confirmed in production. `.retired-6c35` interlock stays parked as the one-command revert. Nothing to revert.
- **★★ LEAD — A2A gates (a)+(b) CODED + verified ([P6-152]).** Design **ratified by Peter** (AskUserQuestion) before any code.
  - **Gate (a) hub-synchronous:** `dispatch()` captures the target's reply inline into the requester's receipt; **no reverse `B→A` envelope** → cycle check needs no reply-exemption.
  - **Gate (b) host-authoritative:** watcher **reconstructs** `hop`/`path` from `ledger.jsonl` lineage per `conversation_id`, overriding agent-asserted values (kept as `hop_claimed`/`path_claimed` + `hop_spoof`). **Closes the TTL-under-report spoof.** Residual (accepted): fresh `conversation_id` resets → daily/fleet caps are the cross-conversation backstop.
  - **Verified 2 ways, £0:** (1) `a2a-watch-test.py` **20/20** (reconstruction, spoof-closure DROP, cycle rejection, fan-in-still-bounded, gate-(a) reply capture via monkeypatched `subprocess`). (2) **Production dryrun-bootstrap** through real launchd (`apply-6c36-…sh`, Peter-run `!`): `runs=1`/`exit 0`; on the real mailbox `cos→pa` allow (host hop1), `pa→cos` **DROP** `cycle` (host hop2, spoof), `pa→pro` allow at **host hop2** vs claimed hop1. Self-restoring (HALT lifted ~6s then auto-restored, booted out, uninstalled).
- **README reconciled.** `shared/a2a/README.md`: L1 note now = host-reconstructed hop/path; "Open" section marks both [P6-142] gates ✔resolved; remaining = agent-side envelope emitter + L3 real-£ units.

## State (SHA-anchored)
- **openclaw.json** `24715f4045bd`/10748B, 600/staff, live==last-good (A-7 clean). **Untouched this session.**
- **Dist patch (persists):** `reply-media-paths.runtime-DegAVXTm.js` = `6dfbfe762078`/6847B.
- **Changed/new (6c.36):**
  - `scripts/a2a-watch.py` `f3460ae17105`→**`96aa0247e52a`**/24246B (433→512L; gates (a)+(b) + header/cycle-comment).
  - `scripts/a2a-watch-test.py` **`cb2496341265`**/10597B (NEW — 20 checks).
  - `scripts/apply-6c36-a2a-dryrun-bootstrap.sh` **`4aecca565e1d`**/4311B (NEW — self-restoring production dryrun bootstrap; Peter-run).
  - `shared/a2a/README.md` →**`c7e32cc09426`**/5844B.
  - `shared/a2a/ledger.jsonl` 0B→3 records (dryrun-smoke evidence; mode=dryrun, never pollutes live).
  - `shared/a2a/HALT` present (fail-closed, restored); `shared/a2a/state/a2a-counters-dryrun-<date>.json` written (dryrun-namespaced).
- **Agents:** `pa`=Marie/opus-4-7, `pro`=Charlotte/sonnet-4-6, `cos`=Andreas/opus-4-7/paired/**A2A-OFF**. Gateway healthy.
- **A2A resting posture:** INERT — HALT present, plist **staged-not-loaded**, `sessions_send`/`sessions_spawn` OFF.

## Carried → 6c.37
1. **★ A2A next increment — the agent-side envelope emitter (before any live flip).** No agent writes an `.a2a-outbox` yet. Wire the AGENTS.md/TOOLS.md convention (hub mints `conversation_id` on the human turn; drop a JSON envelope; **read replies from `results/`, never re-send**) into Andreas (+ Marie/Charlotte as spokes), dryrun over **real agent traffic**, THEN Peter-gated (GUI) flip `A2A_MODE=live` + remove HALT + grant `sessions_send` (hard-gate `sessions_spawn`). The host gates are done + proven; this is the remaining wiring. Kill lever: `touch shared/a2a/HALT`.
2. **Andreas avatar** (first male portrait, §e spec) — optional reversible.
3. **Admin API key** (Peter) → cost-model absolute reconciliation ([P6-148]/[P6-109]); cost-model coverage fix (archived/gzipped trajectories) — Andreas-owned backlog.
4. **`CHAT_RESET_TOKENS`→~70000**; reply-watcher plist reload if pending — optional reversible.
5. **Charlotte handoff** — flows to the briefing via memory-log fallback; a dedicated `workspace-pro/memory/digest-<date>.md` is a one-line brief nudge if Peter wants her curated content verbatim.
6. **Travel agent** next after Andreas (~early Aug, [P6-147]); screenshot delivery (its hard dependency) fixed.

## Discipline
Cost: **£0** — all host-side coding + unit tests + DRYRUN; no billed agent turns, no Opus test-loops ([[no-unauthorized-test-spend]]). No secrets read (structural `shasum`/`ls`/`launchctl print`; `openclaw.json` referenced by sha only, never Read). openclaw.json untouched (A-7 byte-clean). Every change SHA-anchored + reversible; the dryrun bootstrap self-restored the kill-switch (trap-on-exit) and left A2A INERT. The two decisions (design ratification; bootstrap depth) were surfaced to Peter, not assumed. Deviations additive-in-place (P6-152 new; P6-150 appended). Carry-chain audit PASS at open and close.
