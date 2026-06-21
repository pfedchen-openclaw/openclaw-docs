# SESSION 6c.10 — "confirm-fixes + author v7" became a full diagnosis: both assistants unusable → remediation programme + judgement doctrine; v7/Andreas gated behind a trust-foundation build — CLOSURE
_2026-06-20. 6c.10 opened to confirm the 6c.9 delivery fix and author ARCHITECTURE v7. Peter's intervention reframed it: **both Marie and Charlotte are unusable**, they have failed him repeatedly, and the project is at risk of delivering zero value. So 6c.10 became a deep forensic diagnosis (gateway logs + the 14–20 Jun Peter↔agent correspondence + a persona/memory-file audit) → a comprehensive remediation programme → a **locked judgement doctrine** (`JUDGEMENT-DOCTRINE.md`, research-grounded). New codes **P6-67…P6-73**. **No config mutation this session — pure analysis + doc authoring.** ARCHITECTURE v7 and Andreas are explicitly **deferred behind a trust-foundation build** (6c.11) and an acceptance gate (6c.14)._

## §1 The headline — both unusable, two different root causes
- **Charlotte (`pro`) is blocked, not broken.** She genuinely triaged the real Soveren inbox through 18 Jun (archived spam in batches, surfaced real senders, learned rules, sent daily summaries). She is hard-stopped by **two infrastructure blockers**: Google OAuth died 19 Jun (`invalid_grant`) and her Telegram *receive*-offset froze 17 Jun (she hasn't seen Peter's messages since). Unblock both → she returns to useful.
- **Marie (`pa`) has a design/judgement failure.** Even with working tools she does not carry a task to completion: she promises and forgets, claims done without doing, recommends without checking, asks what she could look up, over-generalises one-off choices. Peter, 19 Jun: *"So far you've been useless - not a single task accomplished to the end."* Not a model problem (GPT-5 fallback felt no different); not mainly capability.

## §2 The diagnosis (evidence-verified against logs + transcripts)
- **[P6-68] Anthropic workspace budget cap exhausted (~18 Jun).** Every Opus-4-7 interactive turn rejected (*"reached your specified workspace API usage limits … regain access 2026-07-01"*), silently failing over to `openai/gpt-5` (204 fallbacks on 20 Jun alone). An admin-set *monthly* cap, liftable in the Console — not an Anthropic suspension. Peter: keep the GPT-5 fallback as-is.
- **[P6-69] Marie task-reliability + judgement gap** — promise-then-forget (no durable task store; the 09:30 hotel list lost overnight), hallucinated-done (the immigration-tracker "got it" with no write path), recommend-without-check (the Metz hotel hallucination), premature hand-off (the AbeBooks checkout link), context-blind questions ("you should know when I'm in Paris"), and a one-off "prefer new" risking promotion to a standing rule.
- **[P6-70] Persona/memory audit** — no durable-vs-situational mechanism (root cause); a live Drive contradiction; the harmful "Peter does the checkout himself" memory; missing competence rules; empty daily-memory files during the crisis; AGENTS.md bloat.
- **[P6-71] Infra/comms failures** — F44 OAuth 7-day expiry fired (both agents' Google dark 18–19 Jun); Charlotte's Telegram receive-offset frozen since 17 Jun (the 6c.9 [P6-64] fix was applied to `pa` only); the `heartbeat-watchdog` fallback-alert storm (paging Peter every ~15 min because its dedup is defeated by continuous fallback — spam on fallback, silence on Charlotte's real outage).

## §3 The spend (~£/$200 in 5 days) — mechanism, not exact figure
Console is the $ truth (local estimates overstate — they bill GPT-5 fallbacks at Opus rates). The mechanism is unambiguous and **almost entirely Marie**: **514 billed model calls in 6 days** (peak 157 on 19 Jun) vs Charlotte's **98 total**. Drivers: long failure-prone **browser/tool marathons** (TV Licensing, repeated hotel searches) ending in nothing delivered; **context bloat to 337K tokens** (past the 200K window) → expensive every turn + compaction/reset thrash; **heartbeat ~every 15 min** (~890 beats/day); retry → GPT-5 fallback on the bloated context. **No per-turn cost/tool-call ceiling.** She spent most precisely when failing most.

## §4 Decisions taken (Peter)
- **Payments:** activate the dormant "Marie PA" **Revolut card** — ring-fenced low limit, **approve-first**, value gated at the card.
- **Accounts:** agents may create **any** account on Peter's behalf, provided they **state what was created, write it to 1Password, and log to relevant trackers** (e.g. loyalty programmes).
- **Agent email:** stand up **`marie@fedchenkov.com`**; Charlotte stays on Soveren until the sunset (no @fedchenkov for her).
- **Autonomy:** **approve-first** across new external actions for now (graduate proven classes to avoid confirmation fatigue).
- **Fallback:** **keep GPT-5** as-is (no change).
- **Family remit is Marie's, not Sophie's** — she helps **Olga and Peter's parents** via separate Telegram channels (confirmed in her SOUL/PLAYBOOK).
- **Travel agent before Andreas** (my call, on Peter's trust), but **only after the pair is fixed** through the acceptance gate.

## §5 The judgement doctrine — LOCKED ([P6-72] → `JUDGEMENT-DOCTRINE.md`)
Designed deeply at Peter's insistence and grounded in two completed, cited research streams (elite-EA/CoS craft + production-agent best practice) that **converge**. Core: **principles carry their rationale**; **competence standards (durable) vs. taste (situational)**; a four-tier **act / act-and-inform / surface / ask** ladder keyed on reversibility × stakes × dominance; **≤3 options + a recommendation**; **best = quality × Peter's time, not price**; **own the outcome end-to-end**; **verify before claiming done**; **no surprises / discretion-as-hard-constraint**; a **durable-vs-situational learning gate** (scope-bound, 2+ signals to promote — the fix for the "prefer new" class). Applied to the agent files at 6c.11 via `6c11-BRIEF-EDITS-STAGED.md` (staged, not yet applied).

## §6 The remediation programme + revised sequencing ([P6-73] — supersedes the PRE-COS-EXECUTION-PLAN order)
Eleven workstreams (WS-0 unblock · WS-1 reliability/task-persistence · WS-2 judgement · WS-3 Drive/Sheets write · WS-4 agent email · WS-5 browser robustness · WS-6 payments · WS-7 accounts+credentials · WS-8 alerting · WS-9 cost controls · WS-11 family channels). Sequence:
- **6c.11 — Trust foundation** (WS-0,1,2,8,9): make the pair *trustworthy* before new capability.
- **6c.12 — Capabilities I** (WS-3 Drive/Sheets write, WS-4 email, WS-5 browser) — all riding the owned-GCP/Workspace keystone.
- **6c.13 — Capabilities II, high-trust** (WS-6 payments, WS-7 accounts) — approve-first.
- **6c.14 — Acceptance gate** (real delivered tasks: tracker write, email-a-hotel, book-to-pre-pay, order-to-checkout, create-account+1Password, Charlotte triage+alert).
- **6c.15 — Travel agent** build (before Andreas, per Peter).
- **6c.16 — CAPSTONE: ARCHITECTURE v7 + canonical reconciliation = the Andreas-ready spec** ([P6-76]) — the displaced 6c.10 capstone, re-instated. Authored on the *proven* roster (Marie + Charlotte + Travel), folding in the 6c.10 outputs: the judgement doctrine, the remediation programme, the capability decisions, the [P6-49]/[P6-74] engineering-steward + infra-currency remit, **S11** loop-protection, **S6** schema reconcile, **SR-1/D23** doc-sync, the voice/conventions lock. (Numbers 6c.15/16 indicative.)
- **then 6d Andreas.**
- **Hard gate:** ARCHITECTURE v7 is authored at the **6c.16 capstone**, not before — only once Marie, Charlotte (and Travel) are proven through the acceptance gate. A CoS orchestrating unreliable agents amplifies the unreliability; and the spec is far stronger authored on a working roster than on the broken one 6c.10 would have used.

## §7 End-state (anchors)
- **`openclaw.json`: `d14bb41a3cc4` / 9963 B — UNCHANGED** (no config mutation this session; A-7 clean per `config-health.json`).
- **New docs (this session, in `_session-docs/`):** `JUDGEMENT-DOCTRINE.md`, `6c11-BRIEF-EDITS-STAGED.md`, this closure, `SESSION-6c.11-OPENER.md`. **Deviations log:** P6-67…P6-73 appended (additive-in-place).
- **Live agent files: UNTOUCHED** (brief edits staged for review, applied at 6c.11 on Peter's sign-off).
- **Unchanged:** image `capable-2026-06`; pin **2026.4.22**; gateway gui/502; GPT-5 fallback in place.

## §8 Outstanding / carried (→ 6c.11, ordered)
1. **WS-0 unblock** — re-auth Google (both); the **F44 Internal-OAuth flip** (owned GCP — kills the weekly token death); **Charlotte's frozen Telegram offset**; the **watchdog storm**; **Console cap** (Peter's action).
2. **WS-1 reliability** — durable `TASKS.md`; done-by-verification; full Telegram-history access; memory/context hygiene + daily-log capture.
3. **WS-2 judgement** — apply the staged brief edits + audit fixes.
4. **WS-8 / WS-9** — watchdog redesign (dedup + guaranteed real-blocker escalation); per-turn/task cost+tool ceilings; heartbeat right-sizing.
5. **Capabilities** → 6c.12/13; **acceptance gate** → 6c.14; **Travel before Andreas**.
6. **Standing carries (from 6c.9/PRE-COS-BACKLOG):** [F59]/[P6-66] token rotations (Peter deferred); S6 schema reconcile; SR-1/D23 doc-sync; F64/F68/F70 launchd jobs; 2026.6.6 update (pinned).

## §9 Honest note
6c.9 declared "reliability green" and the agents *still couldn't talk to Peter*. 6c.10's lesson is the same one level up: the platform built *capabilities* and proved them in isolation, but never validated the **end-to-end task loop under real use** — F26 ("done = a real delivered artefact") was learned for pings, never extended to tasks. That is now the organising principle of everything ahead: **a capability is live only when a real task completes end-to-end, verified — and we test that under strict cost discipline, because the whole crisis was spend with nothing to show for it.**

## §10 Post-close addendum — brief edits APPLIED live; self-audit; two new considerations (same session)
- **Brief edits applied, not just staged.** At Peter's instruction the staged doctrine + audit fixes were applied to the **live** files of both agents and verified by direct read (backup `_backups-6c10-brief/`). A pre-existing **stale memory** line (Marie's old "2FA card, graduating ~20 Jun") was corrected to the locked Revolut/approve-first decision. **6c.11 Stage 3 therefore shifts from "apply" to "verify on a real beat + a memory-hygiene sweep."**
- **Self-audit corrections (Peter-requested):** added an **autonomy-overlay** clause to both §Judgement (the act/surface/ask ladder operates *within* the approve-first level — "act silently" ≠ a new external action, closing a premature-autonomy risk); added a **natural-cadence** rule to both §Talking (acknowledge a task then go quiet; update only if delayed/needs-input; don't reply to trivia — Peter's point 2).
- **Model portability (Peter's question):** all scaffolding persists across models — it's framework-injected context, not model-bound; only *adherence fidelity* tracks the model tier (reinforces the principle-based doctrine and the case for Opus 4.8).
- **[P6-74] model + framework currency debt → Andreas owns it.** Pinned to 2026.4.22 (no Opus-4.8) on opus-4-7 — stale on both ends. Andreas's remit ([P6-49]) extended to own infrastructure currency (upgrade cadence + path to an Opus-4.8-capable build, under the update-wipe guardrails; pin-bump surfaced to Peter). **Not bumped now** — stabilise the pair first.
- **[P6-75]** records the live application + the self-audit corrections. Docs re-committed (deviations now P6-67…P6-75).

---
_Attachments for 6c.11: this closure + `SESSION-6c.11-OPENER.md` + `JUDGEMENT-DOCTRINE.md` + `6c11-BRIEF-EDITS-STAGED.md` + `PHASE-6-DEVIATIONS-LOG.md` (P6-67…P6-75) + `PRE-COS-BACKLOG.md`._
