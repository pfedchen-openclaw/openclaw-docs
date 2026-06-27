# SESSION 6c.12 — Capabilities I: Drive/Docs/Sheets WRITE shipped (F26 PASS) + browser over-block diagnosed away — CLOSURE
_2026-06-27. Clean Capabilities-I session on the resumed, trustworthy pair. **SSH context (`launchctl managername` = Background ∴ D20: no gateway restart / LaunchAgent / GUI-consent ops)** — but the headline capability needed none of those. New codes **P6-83/84**. **`openclaw.json` UNCHANGED** (`8823761e1900`/9994B — all work was the bound helper + workspace files). v7/Andreas remain gated behind the acceptance gate (6c.14) — untouched._

## Stage 0 — ALL-GREEN (one correction)
- **A-7 anchor in the opener was stale.** The opener's Stage 0 said live==last-good==`d14bb41a3cc4`; that pre-dated [P6-82]'s live config change. Verified actual: **live == last-good == `8823761e1900`/9994B**, perms 600/staff, `.bak`=old `d14bb41a3cc4`/9963B. A-7 clean.
- Pin **2026.4.22** (`status` flags 2026.6.10 available — pinned, no bump). Both containers `capable-2026-06` (up 9h). Telegram ok; heartbeats enabled (sentinel absent); 30m beats; gateway active pid 50252.
- **Pair healthy:** recent pa turns resolve to `claude-opus-4-7`/anthropic (the two "gpt-5" log hits were 13-Jun config-reload noise, not beats). OAuth **live + durable** (Marie calendar read OK; 0 `invalid_grant`). Watchdog (`ok` every ~15m) + colima-socket-watchdog clean; canary state shows only the [P6-82] resume fire, no re-fire.

## What shipped (reversible, live, F26-verified)
- **WS-3 Drive/Docs/Sheets WRITE ([P6-83]) — F26 PASS.** Six verbs added to `google_helper.py` (`drive-upload`, `sheet-create/-write/-append`, `doc-create/-append`): `15566ad20307`/8396B → **`b38f267ded20`/17648B**. Granted scopes turned out broader than the brief (full `documents`+`spreadsheets`, not just `drive.file`) → native Docs/Sheets read+write. **Posture:** internal-artefact writes ACT directly; **never share/send/delete**; calendar stays prepare-only. **Restart-free:** the helper is an RO **bind mount** into both containers, so a host edit is live with no recreate/restart (the reason this was doable over SSH). **Verified in three layers, cost-disciplined:** free host end-to-end (all six, readback OK, artefacts trashed) → real sandbox `docker exec` write → **real Marie gateway turn** created Doc "6c12 write test" (`1bFuu2…AoS4E`, owner pfedchen@, content read back via API). Embedded-CLI deliberately avoided ([P6-81] lesson). **Docs+memory updated:** `workspace-pa/TOOLS.md` `b407763d591a`, `workspace-pro/TOOLS.md` `4b61c85b0b4c` (verb list + write/gating; corrected the now-false "no write path"/"gog client"/"APIs not enabled" lines); `workspace-pa/MEMORY.md` `ee9284b87600` (stale "no reliable calendar access" line reconciled — opener carry closed).

## What was diagnosed away (no change needed)
- **WS-5 browser "over-block" ([P6-84]) — STALE premise, RESOLVED by evidence.** Live `browser.ssrfPolicy` = `{dangerouslyAllowPrivateNetwork:false, hostnameAllowlist:[]}`. Empirically (free `openclaw browser navigate`): example.com **and** booking.com both render full real content under the empty allowlist. That IS Peter's design philosophy (IP-class block, no domain allowlist). The [P6-39] populated-allowlist gate (+ the [P6-41] SPA-submit block riding on it) was cleared to `[]` since → over-block gone. **No SSRF/routing change, no GUI restart.** Per-site travel recipes stay deferred to the Travel agent (6c.15).

## What was carried (correctly — GUI/Workspace-gated, not skippable over SSH)
- **WS-4 agent email `marie@fedchenkov.com` (Stage 2) → 6c.13.** Needs a fedchenkov.com Google Workspace stood up + a GUI OAuth consent (the Internal graduation) + Peter's domain/admin actions — none doable over SSH (D20). The `gmail.modify` scope is already granted (send-capable), so once the identity exists the wiring is small. This is the trigger to graduate OAuth External→Internal.

## End-state (anchors)
- **`openclaw.json`: `8823761e1900` / 9994 B — UNCHANGED this session.** A-7 clean (live==last-good), perms 600/staff, `.bak`=`d14bb41a3cc4`.
- **Pin 2026.4.22; both containers `capable-2026-06`; gateway gui/502 pid 50252; heartbeats ENABLED (30m, watchdog guarding); GPT-5 fallback in place.**
- **Changed files (SHA-anchored):** `agent-creds/google_helper.py` `15566ad20307`→`b38f267ded20`; `workspace-pa/TOOLS.md`→`b407763d591a`; `workspace-pro/TOOLS.md`→`4b61c85b0b4c`; `workspace-pa/MEMORY.md`→`ee9284b87600`. New docs: this closure, `SESSION-6c.13-OPENER.md`. **Deviations:** P6-83/84 appended (additive-in-place).
- **Artefact left in Drive:** Marie's "6c12 write test" doc (the F26 proof) — harmless; Peter can trash it.

## Honest note
A genuinely clean session: the headline capability (Drive/Docs/Sheets write) shipped and F26-passed end-to-end on the real gateway path, and the secondary objective (browser over-block) turned out to need **no work** — the live posture already matches Peter's intent, which only empiricism (not the carried-forward hypothesis) revealed. Test spend was one sub-dollar Opus turn on Peter's explicit OK ([P6-51] held; inspection/free-API verification did the rest). The one risk averted: editing code that lives in the deny-Read secrets dir was done by copy-roundtrip so no secret value entered context (F21/F51). Net: WS-3 live + verified, WS-5 closed by evidence, WS-4 cleanly teed up for the next GUI session.

---
_Attachments for 6c.13: this closure + `SESSION-6c.13-OPENER.md` + `JUDGEMENT-DOCTRINE.md` + `PHASE-6-DEVIATIONS-LOG.md` (P6-83/84) + `PRE-COS-BACKLOG.md` + `6c11-OAUTH-FLIP-BRIEF.md` (WS-4/Internal graduation reference)._
