# SESSION 6c.27 — CLOSURE

_Reply-watcher notify-flip + generate-mode live-proof, then a long Peter-directed live-incident remediation of Marie (dead browser, session-bloat sticks, payment failure). Peter widened the run mid-session ([[ride-context-dont-close-early]]). GUI/Aqua (D20 — `SSH_CONNECTION` empty, `gui/502` reachable). openclaw.json CHANGED once (sessions_send removal): `eb08cfa7d7b6`→`3267edd8b6aa`/10489B, last-good synced (A-7 clean)._

## What this session did (all [P6-129])
1. **Stage 0 GREEN** — A-7 levers + pin, deviations P6-1…128 gap-free, reset-net byte-match + healthy DRYRUN, reply-watcher live-in-dryrun, P6-128 brief fixes confirmed live, index pa 35·125 / pro 31·36. Carry-chain [P6-101] PASS.
2. **★ Reply-watcher → NOTIFY (Peter-gated, verified).** 1.65-day dryrun: 160 polls, 0 errors, 18 distinct (noise = agent self-sends + newsletters). Peter chose tighten+flip → added `-from:marie@fedchenkov.com` + `MODE=notify` (comment-preserving plist edit `9e6b476f2de7`). launchctl reload deny-listed to ops session → Peter ran via `!`. Verified: notify live, 1 clean send (IVFundAdmin), SEEN dedup works, burst was 1 not ~13. £0.
3. **★ Generate-mode cred store CLOSED ([P6-115]).** Staged a throwaway generate request → `cred-save-watch` drain (45s, launchd) → `op item create --generate-password` → 32-char pw (`1f4cb485a374`/len32) in Agent-Provisioned, value never echoed. Peter deleted the throwaway (`op` mutation deny-listed → `!`); verified gone. First live `mode:generate` proof (04-Jul were existing-mode).
4. **★★ Marie live-incident remediation** (Peter: "both failed… fix this finally"):
   - **Browser dead → gateway restart** recovered it; **proven** on the real Wikimedia £10 URL (efficient snapshot reads `£10 [ref=e7]`).
   - **Payment root fixed:** Marie used **aria snapshots + CSS selectors** on the `browser` tool (both unsupported). Fixed TOOLS.md L102 + reference-tools.md → **efficient snapshot + interact BY REF**, tied to `pay-fill.sh` card handoff. Net-neutral (TOOLS 15698<15765).
   - **Session-bloat "gone quiet":** both pa sessions >120K, compaction hits a **hardcoded 60s timeout** (no config knob) → sticks. Cleared (archive-first) + restarts; re-bloated to 162K in 3.5h → structural.
   - **Exec reflex** ([P6-125] recurrence, /tmp + compound) → TOOLS.md exec note sharpened.
   - **`sessions_send`** dead-tool warning removed from 5 agents via F17 apply script `apply-6c27-drop-sessions-send.sh` (integrity-guarded; `browser` untouched). openclaw.json `3267edd8b6aa`.
   - **Playwright backend** — investigated, **deferred** (Peter: leave as-is): the browser is a dedicated isolated "openclaw" CDP profile that works; switching risks payment-critical login state.
   - **★ chat-bloat auto-heal (Peter's chosen durable fix) — IMPLEMENTED + STAGED:** `chat-bloat-reset.sh` (`93bb06e42841`) resets `telegram:direct` only when bloated(≥130K)+idle(≥20m)+cooldown(≥120m), archive-first, `openclaw gateway restart`; guards unit-tested; plist staged (`497ccbd67145`). **Needs Peter's launchctl bootstrap.**
5. **Marie at close:** provider restarted clean, `:main` lean (63K), fresh telegram:direct, responsive.

## Cost
£0 billed agent turns from the ops session (host-side CLI, inspection, launchd drains only). Peter's own T3/Wikimedia + Marie's background pulses were his/background spend (Console-authoritative).

## SHA anchors (changed/new this session)
| file | before | after |
|---|---|---|
| `openclaw.json` (sessions_send removal, apply script) | `eb08cfa7d7b6`/10654B | `3267edd8b6aa`/10489B (last-good synced) |
| `workspace-pa/TOOLS.md` (browser refs + exec note) | `ab2eb956582b`/15924B | `f519f15fcfe9`/15875B (15698 chars, clip-free) |
| `workspace-pa/memory/reference-tools.md` (refs + pay-fill) | `b76028131e5a`/3894B | `c3594d05c767`/4338B |
| `scripts/ai.openclaw.reply-watcher.plist.staged` + live (notify+exclusion) | `d3ed81e12992`/1858B | `9e6b476f2de7`/2218B |
| `scripts/apply-6c27-drop-sessions-send.sh` (new, F17) | — | `e868eb70c08a`/3270B |
| `scripts/chat-bloat-reset.sh` (new, staged auto-heal) | — | `93bb06e42841`/4939B |
| `scripts/ai.openclaw.chat-bloat-reset.plist.staged` (new, needs bootstrap) | — | `497ccbd67145`/1623B |
- **Live LaunchAgents:** reply-watcher now **notify** (900s, running). chat-bloat-reset **staged-not-bootstrapped**.
- Session archives (archive-first, recoverable): `agents/pa/sessions/archive/2026-07-08/` (:main + 2× telegram:direct + entries).

## Carry-chain check ([P6-101])
- **Mechanical:** P6-1…129 gap-free, no dangling refs; 21/22 header dupes historical. PASS.
- **Carry diff vs 6c.26 "Carried → 6c.27":** reply-watcher notify-flip → **DONE (verified)**; generate-mode live-proof → **DONE (verified, CLOSED)**; brief-budget → **still Andreas (carried)**; reset-net F26 → **armed-live, no live fire (07-05 were manual dryrun tests) (carried)**; media after-window → **confounded by bloat, deferred (carried)**; battery T3/T1/T4/T5 → **T3 attempted by Peter, FAILED on infra (browser+stick), now remediated; re-test carried**; Wikimedia payment → **attempted, FAILED (browser+approach), root fixed; re-test carried**; v7 capstone → **carried (still overdue)**; ANDREAS-ONLY → **untouched, +Playwright-deferred added**. Nothing silently dropped.

## Carried → 6c.28
- **★ FIRST: bootstrap `chat-bloat-reset`** (Peter, GUI/`!`): `cp` staged plist → LaunchAgents + `launchctl bootstrap gui/502 …` + `kickstart -k`. Then verify `tail logs/chat-bloat-reset.log`. Durable fix for the "goes quiet" stick — inert until bootstrapped.
- **Re-test T3 + Wikimedia** now the infra is fixed: message Marie (lean session, no stick) + retry the £10 donation (efficient-refs browser + pay-fill). If they work = real-world confirmation of the payment + responsiveness fixes.
- **Compaction/bloat ROOT stays structural (Andreas):** 60s hardcap + ~25K/turn brief injection drive the bloat; chat-bloat-reset is a self-heal backstop, not elimination. Aggressive frozen-core slice is the real fix.
- **Hard code-level pulse send-gate** ([P6-128]) still open (soft HEARTBEAT rule only).
- **Reply-watcher:** observe notify utility/noise; `poke` only if Peter authorises + scope tight.
- **v7 CAPSTONE / pre-6d consolidation ([P6-76]/[P6-127]) — OVERDUE**, milestone gate before Andreas. Canonical docs stale since 2026-06-11.
- **⛔ ANDREAS-ONLY:** aggressive slice + verify turn; dreaming; `:telegram:direct` compaction bounding; brief-budget; Playwright backend swap (added 6c.27). Unchanged.
