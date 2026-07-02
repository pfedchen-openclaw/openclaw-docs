# SESSION 6c.17 — Two-week frustration review → behavioural remediation + battery scored — CLOSURE
_2026-07-02 (opened 21:45, ~1h after 6c.16 closed). **SSH — no gateway restart / LaunchAgent change / config mutation this session.** Peter reframed the session with real lived feedback from using Marie + Charlotte. Delivered: mined both agents' 2-week transcripts, root-caused the frustrations, applied reversible brief fixes for his explicit asks (check-Sent-before-surfacing; daily-summary redesign) + the rest of the frustration set, scored the acceptance-gate battery by inspection (NOT PASS — remediation loop as designed), found + partly fixed a bootstrap-truncation regression, and surfaced the one real decision (Marie's transactional autonomy). New codes **[P6-92/93/94]**. `openclaw.json` **UNCHANGED** (`8a6e2a19d9d5`/10096B, A-7 clean). **Zero billed agent turns** — all inspection + file edits._

## What Peter asked (his message) → what shipped
1. **"Run my requests past Marie/Charlotte… (didn't run the buy one, Marie got stuck on the Golf England payment)"** → scored the battery T1–T6 by inspection; **T4 Golf/iGolf diagnosed** — England Golf membership is iGolf (£47/yr, pay-to-join, no guest checkout); Marie stalled on the L1 pay-click + no-card wall, handled it honestly (found slots, surfaced the URL) but couldn't finish. **Capability block, not a judgement fail.**
2. **"Make sure agents read my sent emails before surfacing items I've already resolved"** → **check-Sent + reconcile-before-surfacing** rule added to **both** agents' AGENTS.md (+ Marie HEARTBEAT step 1; Charlotte's weak HEARTBEAT-only line strengthened). Plus a **not-interested/handled ledger** and **archive-opt-in** (a real thread is archived only on explicit say-so; Charlotte: archive = spam only).
3. **"Change daily summaries — not helpful"** → **digest redesigned** as a decision aid for both (lead with ranked what-needs-you + a recommendation/draft each; reconcile against Sent; verified items only; "nothing needs you today" is a valid one-line digest; delivery tracked in TASKS.md with proof-of-done). Removed the metrics/archive-bookkeeping/FYI-tail/recycled-backlog format Peter's verdict called out.
4. **"Review my last 2 weeks and resolve the frustrations"** → full ranked inventory (P6-92); each theme fixed or surfaced.

## Root causes found + fixed
- **Judgement failures** (resurfacing handled items, false clashes, acting on implied instructions, activity-log digests) → **remediated in-brief**, placed in the truncation-protected **AGENTS.md** so they persist (both agents), logged in both DECISIONS.md.
- **Bootstrap-file truncation regression ([P6-93])** — the deep reason "she forgets what I told her." TOOLS.md (grown to 15–19KB) + MEMORY.md truncate in live sessions (MEMORY.md → **948 chars**) under the 120K cost-cap + the never-reset Telegram session; the briefs told agents to write corrections to MEMORY.md — exactly the file that vanishes. Fix: durable rules now live in AGENTS.md (protected); **TOOLS.md slim in flight** (verbose prose → PLAYBOOK, dup removed, invocations preserved); config tension surfaced.
- **Notification fatigue** (Charlotte's 24× identical blocker) → de-dup rule.
- **Doc-currency:** Marie AGENTS.md:16 stale "Drive is READ-ONLY" corrected (P6-83 read+write is live); "beats every 30 min" → 2h (P6-85).

## Acceptance-gate battery — scored by inspection (zero billed), scorecard updated
T2 email-as-marie@ **PASS** (real WS-4 send, confirmed landed) · T4 order-to-checkout **BLOCKED** (capability) · T1 durable-reminder **FAIL** (missed timed fire; ⚠ historical fake-close, now [P6-78]-guarded; live re-test = rental reminder due Fri 3 Jul 19:00) · T3 calendar-research **PARTIAL→FAIL** (read herself good; proactive clash mis-read twice) · T5 account+1Password **FAIL** (capability) · T6 Charlotte triage **FAIL** (~4/10). Organic: Josephine move + Wolseley/Elder-Press **PASS**; unprompted archives **FAIL**. **Verdict: NOT PASS — remediation loop.** Two blocker classes remain: **capability/trust** (transactional, T4/T5 — Peter's decision) and **reliability** (timed reminders/digests, T1). Judgement class remediated this session; re-run under the real Telegram path next window.

## Surfaced to Peter (decisions — put via AskUserQuestion at close)
1. **Marie's transactional autonomy** (the #1 frustration): provision the capped "Marie PA" card so she completes checkouts within the £200/mo cap (deferred L3+ step; 2FA-approve-per-charge) · vs keep prepare-and-handover, smoothed · vs let her finish free account-creation only (payments stay hand-off).
2. **The stuck golf items:** set up England-Golf/iGolf membership + the Pro Agenda golf-lesson login (into warmed Chrome + 1Password) so she can drive them to pre-pay, or leave.
_(Also informed, not asked: the 120K-cap ↔ brief-fidelity cost/quality tension — default is the file-slim done this session + monitor; escalate to session-compaction / cap-raise if truncation persists.)_

## End-state (anchors)
- **`openclaw.json`: `8a6e2a19d9d5`/10096B** — A-7 clean, live==last-good, 600/staff. Pin 2026.4.22; sandboxes `capable-2026-06`. **No config mutation.**
- **Brief edits (direct):** pa AGENTS `a6aba8f23e51`→`0486c8c5f03d`/14347B · HEARTBEAT `3dddeb41b879`→`74ec8c4a45a1` · PA-PLAYBOOK `cfe9c034b2cf`→(digest §; +browser-prose from slim — re-anchor) · DECISIONS `dd0839fd21ba`→`58145316036a`. pro AGENTS `7b0db6f70aae`→`0f78a4132f1b`/12910B · HEARTBEAT `0bdef99de68a`→`1c9578cfd97e` · PRO-PLAYBOOK `a16c9e24336b`→(digest §; +prose — re-anchor) · DECISIONS `1f121e417498`→`97fc83f5fc65`.
- **TOOLS.md slim (subagent):** pa/pro TOOLS.md + both PLAYBOOK re-anchored at 6c.18 open (verbose prose moved to PLAYBOOK, invocations preserved).
- **Scripts:** `overnight-verify.sh` `57f9883a654c`→`4f8462f0f30b`/5815B (dryrun no longer pollutes the log). **Deviations:** [P6-92/93/94] appended additive → log `78a6053c71d7`→`93976b5fb8b3`/234323B.
- **New docs:** scorecard updated (battery 6/6 scored). Backups of edited briefs in scratch `6c17-brief-backup/` + `6c17-tools-backup/`.

## Carried → 6c.18
- **Read the real overnight verdict:** `logs/overnight-verify.log` for the night-of-02→03 07:15 fire; confirm it reached Peter + the one-shot self-removed (plist gone, `launchctl list` empty). If FAIL/PARTIAL, diagnose (P6-94).
- **Act on Peter's transactional-autonomy answer** — if "provision the card": the auth-profiles/2FA-pause build (GUI/1Password); if "golf": set up the iGolf + Pro Agenda logins.
- **Anchor the TOOLS.md-slim SHAs** (pa/pro TOOLS + both PLAYBOOK) and confirm no capability lost (grep verbs).
- **Re-run the remediated battery** through the real Telegram path (cheapest model, within the ~$5 gate authorisation) to convert the judgement-class FAILs → PASS; the capability class (T4/T5) waits on the autonomy decision; T1 reliability = the rental-reminder live test.
- **Monitor the truncation canary** (events/day) after the file-slim; escalate config if it persists ([P6-93]).
- Standing: v7 capstone/reconciliation; Andreas-owned org OAuth app; DMARC tighten; 2026.4.22 pinned; model/framework currency → Andreas.

## Honest note
Peter's message turned the acceptance-gate window from a scheduled probe into the real thing: he used the agents, and told us where they hurt. The gate verdict is an honest **NOT PASS** — but the failures split cleanly. The judgement failures (the ones that read as "useless" — resurfacing what he's handled, wrong clashes, unhelpful summaries) were real, recurring, and are now fixed *in the file that survives truncation*, which is the part prior fixes got wrong: rules written to MEMORY.md were being silently cut from her context, so she genuinely never had them. The remaining failures are not judgement — they're a capability wall (she cannot complete a purchase at L1, holds no card) and a reliability gap (timed fires). The first is Peter's call to make (his money), so it's surfaced, not guessed; the second now has the durable-task ledger under it and a live test tomorrow. Nothing was marked done that wasn't: the battery is scored against real artefacts, the brief edits are anchored, and the one thing I couldn't finish — the overnight verdict — is carried honestly because it physically hasn't fired yet.

---
_Attachments for 6c.18: this closure + `SESSION-6c.18-OPENER.md` + `PHASE-6-DEVIATIONS-LOG.md` (through P6-94) + `ACCEPTANCE-GATE-SCORECARD.md` + `ACCEPTANCE-GATE.md`._
