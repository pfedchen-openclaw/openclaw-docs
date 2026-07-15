# OpenClaw Agent Team — Master Architecture v7
## Peter's Personal AI Operations Platform — Phase-6 as-built consolidation

_**What v7 is.** An additive successor to `MASTER-ARCHITECTURE-v6.md` (frozen 2026-06-11, session 6a.35). v6 remains the tombstone for the **unchanged design intent** (the 9-agent vision, Part 1 product decisions, the persona/voice/curriculum material, the pre-Monday build history). v7 supersedes v6 wherever Phase-6 reality has moved on, and it adds the nine subsystems Phase-6 built that v6 never described. **Ground-truth rule (unchanged): where a doc and the running system disagree, ground-truth wins** — this doc, `PROJECT-STATE-OF-NATION.md` (status), `PROJECT-RUNBOOK.md` (ops), and `PHASE-6-DEVIATIONS-LOG.md` (the P6-N record) are the live tier; v6 is the archived design tier._

_**Anchoring.** Every subsystem cites its `P6-N` evidence in the live log. Status vocabulary: **LIVE** (exercised end-to-end through the real runtime, F26-grade), **interim** (a working backstop; the durable fix is deferred), **deferred-Andreas** (owned by the unbuilt CoS, designed here / built in 6d), **surfaced** (a Peter decision is pending or taken)._

- **Hardware/runtime:** dedicated Mac Mini M4, macOS Tahoe. OpenClaw **2026.4.22 (build 00bd2cf), pinned** (predates Opus 4.8; agents run `opus-4-7`/`sonnet-4-6`). Colima container runtime.
- **Config anchor at authoring:** `openclaw.json` `3267edd8b6aa` / 10489 B, 600/staff, A-7 clean (live == last-good).
- **Session:** authored 6c.31, 2026-07-09. Reconciled from **P6-1…138**.
- **Companion docs:** `MEMORY-ARCHITECTURE-v2.md`, `AUTONOMOUS-PAYMENTS-DESIGN-6c19.md`, `PRE-COS-BACKLOG.md`, `CAPABILITIES-ROADMAP-TO-6D.md`, `ACCEPTANCE-GATE-SCORECARD.md`, `AVATAR-TEMPLATE.md`, the derivation drafts `BRIEF-DERIVATION-SOPHIE.md` / `BRIEF-DERIVATION-ANDREAS.md`.

**New/amended in v7 vs v6 (manifest):** PART 0 reconciliation map · PART 1 live roster + gate status · PART 2 the security spine (host-mediated secrets — the throughline) · PART 3 the 2-tier browser model · PART 4 gate-at-value autonomy + payments prepare-and-surface · PART 5 reliability & self-heal (durable-task ledger, chat-bloat-reset net, honesty/mirror) · PART 6 memory-v2 · PART 7 cost architecture · PART 8 the LaunchAgent fleet · PART 9 the S-code subsystem registry incl. the **full S11 loop-protection design** · PART 10 the SR-code autopilots incl. the **SR-1 doc-autopilot spec** · PART 11 the deferred-Andreas track · PART 12 evidence index.

---

# PART 0 — WHAT CHANGED SINCE v6 (reconciliation map)

| v6 section | v7 disposition |
|---|---|
| 1.x Context & product decisions | **STILL-CURRENT** — v6 authoritative for design intent. v7 amends only pins/dates. |
| 1.5 Timeline / PART 6 Implementation / PART 7 Pre-Monday / Appendix A | **SUPERSEDED** — all in the past; and they still carry the pre-reconciliation v5 posture (auto-login, dedicated Apple ID, **Docker Desktop**) that the as-built D11/D17/D25 corrections reversed. v7 records the correction once (PART 8 / PART 1); treat those v6 parts as history only. |
| 2.5 Agent Roster (9 agents, `[TBD]` names) | **PARTIALLY-STALE** → PART 1. Marie/Charlotte LIVE; Andreas/Sophie named + derived (PART 9/§briefs); the other five remain design-time. |
| 3.1 Memory (3-layer, OpenAI-embeddings v1) | **SUPERSEDED** by PART 6 (memory-v2 — the native stack, activated not rebuilt). |
| 3.3 Progressive Autonomy (L0–L5) | **AMENDED** by PART 4 — the ladder is now an *overlay within* a gate-at-value / approve-first posture, not the governing model. |
| 3.7 Heartbeat & Cron | **SUPERSEDED** by PART 7 (cost) + PART 8 (the real launchd fleet — v6's cron list was aspirational). |
| 3.8 Inter-Agent Coordination (Tier 1/2/3) | **EXTENDED** by PART 9 §S11 — v6 named "loop protection" as a line item; v7 designs it. |
| 4.1 Security / 4.2 Payments / 4.4 Web Search | **SUPERSEDED** by PART 2 (security spine), PART 4 (payments prepare-and-surface), PART 3 (2-tier browser). v6 had no host-mediated secret-fill and a single-browser model. |
| 4.9–4.12 Infra (config repo, Colima, DR, boot) | **STILL-CURRENT** (as-built annotated), except the Docker-Desktop restoration steps → PART 8. |
| PART 5 openclaw.json template | **STALE** — config has drifted ~19 sessions (P6-85 levers, media caps, drop-sessions-send). The live file is the truth; v7 does not re-embed a template. |

---

# PART 1 — CURRENT REALITY SNAPSHOT

**Live roster (2 of 9):**
- **Marie (`pa`)** — personal assistant, Telegram DM. Model `opus-4-7` (interactive) / `sonnet-4-6` (`heartbeat.model`). **LIVE and graduated**: own send-mailbox `marie@fedchenkov.com`, calendar write, Drive/Docs/Sheets write, two capped virtual cards (prepare-and-surface), and account-creation **cleared under gate-at-value** (P6-136). Personal domain only; work calendar read-only for clash detection.
- **Charlotte (`pro`)** — professional assistant, Telegram DM. Model `sonnet-4-6`. **LIVE but deliberately narrow**: v1 remit = the sunsetting Soveren inbox triage only (read/label/archive, **no send path at the tool floor**); the rest of her brief is authored-but-dormant until a real `@fedchenkov.com`/`@hylean.com` work mailbox and Peter widens the remit (reverts to Opus at v2).
- **Andreas (`cos`)** — Chief of Staff, **derived not built** (next, 6d). Design: PART 9 §S11 governance + PART 10 §SR-1 + `BRIEF-DERIVATION-ANDREAS.md`.
- **Sophie (`fam`)** — family coordinator, **derived not built** (~6e). Design: `BRIEF-DERIVATION-SOPHIE.md`.
- SDR / Travel / Research / Finance / Librarian — design-time (v6 PART 2.5).

**Acceptance/usefulness gate:** **PASS on all agent-quality dimensions** (6c.30) — T1 reliability, T2 email-as-marie, T3 calendar, T4-drive, T5 account-create-mechanism + gate-at-value, T6 triage, memory-v2, honesty/mirror all pass. The sole residual is the **transactional-completion last-mile** — CAPTCHA-solve (P6-39) + hosted-field cross-origin card-fill (P6-135) — which is external Andreas-class infrastructure, mechanisms proven up to the wall, **not an open agent-quality failure**. Detail: `ACCEPTANCE-GATE-SCORECARD.md`.

**Sequence to 6d:** acceptance gate ✔ → **v7 capstone (this doc)** → pair Andreas. Do not build Andreas against unreconciled docs (P6-76/P6-127).

---

# PART 2 — THE SECURITY SPINE: host-mediated secrets

The single organising principle of the Phase-6 security model: **secrets live host-side only; the sandbox and the model never see a secret value.** Every capability that touches a credential is built so the *host* performs the secret step and the agent orchestrates around an opaque reference.

### 2.1 The boundary
Each agent runs in a Colima sandbox (`mode:all`, `network:bridge` — egress open, secrets stay outside the container; the "Strategy-2" host-network dead-end was abandoned). The sandbox deliberately **has no `op` binary** — a compromised container can never hold the whole-vault token (P6-88). Claude Code's own operator shell is likewise **F21/F51 deny-blocked** from reading/writing `openclaw.json`, `agent-creds/**`, `auth-profiles.json`, `secrets.json`, `credentials/**` — secret-touching edits route through Peter (`!`) or a launchd drain, never around the deny-list (P6-27/P6-83/P6-115/P6-136).

### 2.2 Host-mediated secret-fill (the scripts)
- **`op-get.sh`** — host retrieval, single-vault-enforced (refuses non-OpenClaw), env→file token order, display-immune `--verify` (sha256-12 + length, never the value).
- **`op-put.sh`** — account *creation*: `op item create --generate-password` into the **Agent-Provisioned** vault, tags `agent-created`/`agent:<x>`; `--password-env` to store an existing secret safely. (Bug fixed P6-110: `#!/bin/zsh` `$USERNAME` is a zsh special-param → renamed to `ACCT`.)
- **`pay-fill.sh`** — host card-inject: `--card uk|intl` → op-get by UUID → `browser fill --fields-file` (0600, off argv, trap-unlink); `--dry-run` display-immune. **⚠ Known PATH bug (P6-136):** hardcoded PATH lacks `~/.npm-global/bin` → `openclaw: command not found` (same class as the cred-fill bug fixed live 6c.30 — **fix before the payment work**). **⚠ Known limitation (P6-135):** the single-`--target-id` model cannot fill **hosted-field processors** (Gr4vy/Stripe-Elements/Braintree/Trust-Payments) whose card fields are separate cross-origin PCI iframes → deferred-Andreas.
- **`cred-fill.sh`** — the create-side twin: resolves an op-generated password by `--op-ref op://Agent-Provisioned/<id>/password` via `op read`, fills into `--password-ref` off-argv, umask 077; least-privilege guards refuse any non-Agent-Provisioned ref / non-`/password` field. **Proven E2E** (P6-136): filled a 32-char generated password into a live Letterboxd signup "without Marie ever seeing it".
- **`cred-save.sh` + `cred-save-drain.sh` + the `cred-save-watch` LaunchAgent** (45s) — the sandbox→host credential-SAVE bridge. The agent drops a **non-secret** request JSON into `workspace-<id>/.cred-queue/pending/` (P6-114: `shared/` is NOT mounted into the sandboxes, so the queue lives in each agent's own `/workspace`); the host drains it, runs op-put, writes a display-immune receipt. `mode:generate` (host generates+stores) and `mode:existing` (Peter-supplied, redact-shred after store); any `password/secret/cvv/token/pin/otp` key is fail-closed refused unless mode:existing. Both modes proven live (P6-115/P6-129); idle-log-spin fixed (P6-126).

### 2.3 1Password service-account
An SA (`openclaw-agents`) gives unattended `op read` with no interactive signin (resolves F70). Token in a 0600 `agent-creds/op-service-account.token` (deny-Read), sourced by the helpers, removed from the 644 `.zshrc`. Two vaults: **OpenClaw** (ops secrets) and **Agent-Provisioned** (agent-created account logins) — separated so agent-created accounts never sit beside operational credentials (P6-88).

### 2.4 Config hygiene (F17) + integrity (A-7)
`openclaw.json` edits ONLY via a staged `.new` in the same dir → atomic same-dir `mv` → `chmod 600`/`chgrp staff` in the same block; `.last-good` kept byte-identical after a clean apply (A-7); SHA-anchor before/after. `openclaw config set` (schema-validated, atomic, perms-preserving) is the sanctioned non-gated path. Never edit in place; never stage in /tmp.

### 2.5 Sandbox binding + the inode-pin gotcha (P6-113 — load-bearing)
Single-file bind mounts pin the **inode at container creation**. An F17 atomic-rename update creates a *new* inode → the running container keeps the OLD one until `sandbox recreate`. This silently truncated both agents' Gmail helper (stale inode, zero-output exit 0). **Rule:** any atomic-rename update to a bind-mounted FILE requires `sandbox recreate --agent <id> --force` **after the host fix**, and F26 verification must run in the freshly-recreated container. Directory binds re-resolve; single-file binds do not. RO-bound `agent-creds/` helpers can be `cp`'d in place then recreated (no gateway restart); a NEW bind needs a gateway restart (D20). Per-agent sandbox blocks must be re-synced on any `agents.defaults.sandbox` change (P6-24). Host-loopback reachability (`host.lima.internal` → gateway :18789 + SSH :22) holds because the control API is token-gated and the token lives outside the container (P6-35).

---

# PART 3 — THE 2-TIER BROWSER MODEL

Two browser paths, routed by the injection-trust of the content, not convenience.

- **Tier-1 — host `browser` tool** (primary). The agent calls a high-level `browser` tool; the **gateway** drives a real **Google Chrome** on the host over loopback CDP (`127.0.0.1:18800`) in a dedicated persistent **`openclaw` profile** (warmed, headful, residential UK IP — never Peter's personal Chrome). The container never touches CDP (so no P6-35 SSRF re-opening). Verbs: navigate/snapshot/screenshot/fill/type/select/press/click/evaluate. Passes anti-headless fingerprinting (P6-39). LIVE (real BA/Booking/Aman turns).
- **Tier-2 — in-container `browser_helper.py`** (Playwright over image-baked Chromium). Reads/nav FREE; interactive `fill-and-shot` screenshots the **pre-submit** state and never commits; `commit --do SEL` is a separate post-approval action. **Script-level guards:** `--fill` refuses password/payment fields; `--click` refuses commit-looking buttons. Egress guard refuses loopback/private/link-local/metadata/host targets for both top-level nav and programmatic `fetch()` SSRF (P6-34/P6-35). Used for untrusted third-party links.

**Gotchas that constrain agents (must survive into any new agent's brief):**
- **`refs=aria` gap:** Tier-1 requires interaction **by ref from an `efficient` snapshot**; `aria` snapshots need a Playwright-*launched* Chromium (`_snapshotForAI`), absent because we drive system Chrome over CDP. Swapping to Chrome-for-Testing risks the persistent login state → deferred (P6-129/P6-135).
- **`fill` shape:** one field = `type {ref,text}`; multi-field = `fill {fields:[{ref,value}]}`; `fill {ref,text}` is INVALID (was a live payment blocker — P6-131).
- **`navigate` not `open`:** host CLI `open` (new tab) is fail-closed SSRF-blocked; `navigate` (current tab) succeeds to the same public URL (P6-111).
- **Validation false-positive:** `agents.pa.tools.allow` warns `browser` unavailable, but the tool dispatches fine — benign (P6-132).

**Enabling host-browser for a NEW sandboxed agent = three layers + recreate** (P6-39/P6-99): (1) `tools.alsoAllow += "browser"`; (2) `sandbox.tools.alsoAllow += "browser"`; (3) `sandbox.browser.allowHostControl = true`; then gateway restart + `sandbox recreate --force`. Done for pa+pro only, never global.

---

# PART 4 — AUTONOMY: gate-at-value + payments

### 4.1 Gate-at-value (the governing model; replaces enumeration)
Autonomy is a **choke-point / gate-at-value** rule, not an enumerated allowlist — applied on two fronts:
- **Browser egress (P6-42):** renounced the "trusted-websites" hostname allowlist (the open web can't be enumerated; it had blocked Trenitalia). Now **all public hostnames allowed, all private/internal/link-local IPs blocked** — IP-class egress filtering at the choke point. Strictly more secure than the `dangerouslyAllowPrivateNetwork` escape hatch (which is all-or-nothing across both SSRF gates).
- **Actions (P6-136, Peter's "gate at value"):** an agent **creates + saves accounts autonomously** for free/reversible signups, and surfaces-before-submit **only** at genuine choke-points — (a) any payment/pay-click (never auto-charge), (b) government-ID / strong-identity / financial-account-linking signups, (c) consequential legal T&Cs. A choke-point rule, not a category list. Sonnet-validated 4/4 (newsletter=DO, £47 golf=SURFACE, NHS-ID=SURFACE, Goodreads=DO).

**Design philosophy (P6-45, `security-design-philosophy`):** gate **value at the card issuer, not in the agent** — a dedicated virtual card with low per-tx/monthly caps + quasi-cash MCC blocks. The browser allowlist never defended value-exfiltration (attackers use reputable would-be-listed sites); that defence belongs at the payment layer. Never fully remove the spend confirmation; raise its threshold as trust grows. The L0–L5 ladder (v6 §3.3) now operates as an **overlay within** the current **approve-first** posture (P6-73/P6-75) — "act silently" never means a new external action.

### 4.2 Payments — prepare-and-surface (the hard floor)
**Never-auto-charge.** v1 = the agent assembles a complete checkable proposal (payee, amount+currency, what-for, exact card), drives login/checkout in the isolated `openclaw` Chrome profile, and **surfaces**; Peter completes the pay-click + 2FA/OTP (P6-3/P6-87/P6-99). For OTP, Peter relays the code and the agent inputs it (single-use, never a password). Card retrieval is host-only via `op-get` at the pre-payment screen; the card is filled by the host **by ref** via `pay-fill.sh` **only while the agent is paused** (surfaced) — so no agent browser-read turn ever sees the digits. Cards: UK Revolut virtual (£200/mo issuer cap) + N26 international; rule UK→Revolut, non-UK→N26, in-doubt→ask.

**F26 status (honest):** prepare-and-surface is F26-PASS on real turns ("pay Thomas £45" correctly refused auto-pay and flagged the £45-vs-€45 mismatch; Wikimedia £10 drove-to-checkout-and-stopped). Autonomous plain-form fill is LIVE. **Real-charge autonomous completion is NOT done** — blocked by hosted-field iframe processors (pay-fill's single-target reach) and CAPTCHA. Both = deferred-Andreas (P6-111/P6-135). Full model: `AUTONOMOUS-PAYMENTS-DESIGN-6c19.md`, `agent-payments-autonomy-model`.

---

# PART 5 — RELIABILITY & SELF-HEAL

### 5.1 The durable-task ledger (P6-78)
A per-agent `TASKS.md` row-model ledger with a **mandatory proof-of-done field** (the exact artefact confirming completion). Rules: "the write is the acknowledgement" on accept; read every beat; fire on overdue/committed; a row leaves Active only when its proof is re-confirmed. This operationalises done-by-verification (F26-for-tasks) — the "never claim done until the artefact exists" maxim now has a file mechanism. Built as the structural fix to the crisis's organising failure (promise-then-forget, hallucinated-done, the lost 09:30 shortlist — P6-69). Write-on-accept is AGENTS.md-hoisted (always-loaded) so interactively-given tasks don't slip (P6-81). Verified live: the 20:00 reminder fired at 19:57 and the row was correctly held OPEN pending Sent-proof — no fake-done (P6-138). One-message→one-row-per-ask, 3-task PASS (P6-132/P6-135). **Exposed gap:** outbound-bounce detection — a British Gas complaint bounced ("550 Access denied") and sat silently "sent"; interim fix = a HEARTBEAT beat-1 bounce scan that re-opens the task (P6-138); robust reply-watcher DSN sweep = deferred-Andreas.

### 5.2 The chat-bloat-reset self-heal net
A layered set of host scripts + LaunchAgents that heal the **session-bloat / stick** pathology — the crux failure where long-lived sessions defeat compaction and wedge the agent. All archive-first (recoverable), never mid-conversation, cooldown-guarded.
- **`chat-bloat-reset`** (900s) — archives+clears `telegram:direct` ONLY when `totalTokens ≥ CHAT_RESET_TOKENS` (130000) AND idle ≥ 20m AND last-reset ≥ 120m, then gateway restart. **FIRED its first live cycle, F26-confirmed** (SKIPped during a live turn, RESET once idle crossed threshold — P6-132).
- **`session-freshstart`** (06:55) — daily archive+clear of `:main` ONLY (never telegram:direct — preserves Peter's chat continuity, P6-95), quiet-window/active-skip guards, then restart. `--agent` selector (P6-121).
- **`heartbeat-watchdog`** (900s) — the reliability daemon: stall-restart, real-blocker escalation (OAuth-death → outbound via a working channel), a cost/context canary, and **intra-day `:main` auto-reset** on `inputTokens ≥ 250K` with no live human chat (P6-121). Cooldown-throttled; the auto-reset D-net has not yet fired on a genuine production breach (only fixture/DRYRUN) — the good case (P6-123/P6-126).
- **`heartbeat-nightwindow`** (23:00/07:00) — overnight beat suppression, with manual-pause precedence so it never re-enables over a P6-77 bleed-stop.

**The residual pathology (P6-119/P6-125):** the daily freshstart deliberately excludes human `telegram:direct` sessions, so those grow unbounded → the **256 KB whole-prompt byte-cap truncation** (`limitBytes=262144`, distinct from token-budget compaction) that dominates context-loss (631/677 truncations lived in two un-reset sessions), and on Sonnet the compaction of such a session can hit a hardcoded 60000ms×4 timeout and stick `processing` ~7min. `chat-bloat-reset` is the **backstop**; the **CURE** (aggressive frozen-core brief slice + session bounding) is deferred-Andreas (§A.4). The `P6-77` bleed-stop (a `logs/heartbeat.paused` sentinel the watchdog re-asserts every 15m) freezes all beats during a degraded/cost-bleeding period.

### 5.3 Honesty / mirror / brief rules (the fabrication-class cure — soft)
A cluster of durable rules in the **truncation-protected always-injected files** (AGENTS.md/SOUL.md/HEARTBEAT.md), placed there after P6-93 showed MEMORY.md-placed rules silently vanish:
- **No-invented-facts (P6-128):** never assert a fact about Peter not sourced from the known set — generalised from the London Clinic incident (a pulse autonomously emailed a false "Peter will bring private medical insurance" claim; he self-pays).
- **Receipt rule (P6-102):** no completed-action claim without an in-turn successful tool result; on-screen flash ≠ committed.
- **Mirror rule (P6-103/P6-104):** never dis-claim real completed work because a tool isn't attached on a flush/restricted-tool turn — worked exactly as designed in P6-134 (refuted a false self-accusation on the real CeFoLiAc send).
- **No-fake-done / report-from-result (P6-134):** report a send's to/cc from its *result*, not the flags passed.
- **A-beat-drafts-never-sends (P6-128):** a pulse drafts+surfaces, never sends an external commitment even if a TASKS.md row says "send". **Still SOFT (brief-only)** — the hard code-level pulse send-gate is unbuilt (deferred-Andreas).
- **Send-boundary hard floor (P6-84/P6-125):** `gmail_helper.py` `SEND_IDENTITIES={marie@fedchenkov.com}` — `_require_send_identity` hard-refuses any non-send-identity cred, so pfedchen@ + Soveren are draft-only **at the tool floor** (no prompt can route a send through them). The marie@ surface-then-send discipline is AGENTS.md policy, not a second hard gate (a hard approval-token stop = deferred-Andreas).

Root cause of the fabrication class is **infra** (stripped tool inventories on flush turns, un-reset-session tool-attachment inconsistency), so the brief rules are labelled **coping-not-cure** (P6-102/P6-116/P6-128).

---

# PART 6 — MEMORY-v2

**The reframe (P6-116→P6-118):** the crisis "root flaw" was believed to be a missing retrieval layer; in fact OpenClaw 2026.4.22 **natively ships the full retrieval/consolidation stack — it was switched OFF** (`Indexed 0/34`, `Recall store 0`, `Dreaming off`). v2 is mostly **activation + wiring + file restructuring of machinery already owned**, not net-new engineering. Full spec: `MEMORY-ARCHITECTURE-v2.md`.

- **Retrieval/index stack — LIVE.** Hybrid embedding + FTS (`memory_search`/`memory_get`); working/episodic/semantic split (session / `memory/YYYY-MM-DD.md` / `MEMORY.md`); recency decay; idempotent pre-compaction flush. Index ON: pa 39/39·314 chunks, pro 35/35; sqlite persists across restarts/freshstart/reboots. F26-by-inspection: the Porsche query that returned `count:0` on the failure day (causing a lost-to-compaction rebuild) now returns at 0.667.
- **Config levers applied (6c.22/6c.23):** `compaction.model=sonnet-4-6`, `keepRecentTokens=40000`, `truncateAfterCompaction=true`, `contextInjection=continuation-skip`, hybrid MMR + temporalDecay (halfLife 21d); **media caps** `browser.snapshotDefaults.mode="efficient"` (binary, not enum), `imageMaxDimensionPx=1024`, `pdfMaxPages=15`, `pdfMaxBytesMb=8`. Dreaming stays OFF (auto-promotion opaque → deferred-Andreas). `toolResultMaxChars` left at 16000 (already tight — the review's "biggest lever" was false). `params.cacheRetention` and a `compaction.timeout` key do **not exist** on 2026.4.22.
- **Daily-file dedup (P6-119):** the non-idempotent memoryFlush re-appended blocks 9× → exact-block dedup removed 117 dup blocks (pa 07-03 183KB→86KB).
- **memory-health canary (P6-119):** `memory-health.py` + LaunchAgent (23:55) — per-turn tokens, truncation rate, compaction count, cache ratio, dup-heading, index freshness, retrieval-usage, stale-session detector. Log-only; Telegram ALARM on breach only.
- **Bootstrap-brief truncation (P6-36/P6-93/P6-98):** injected files share one `bootstrapTotalMaxChars` budget in fixed order (AGENTS→SOUL→TOOLS→IDENTITY→USER→HEARTBEAT→MEMORY); once the budget runs low the tail files were **silently skipped** ("she forgets what I told her"). Fix: `bootstrapTotalMaxChars` 60000→110000, `bootstrapMaxChars` 30000/file. pa's always-injected brief now sits **at the ceiling** — any growth requires displacement (a CoS brief-budget item). Only recognised filenames are injected; PA-PLAYBOOK/DECISIONS/TASKS/USER-CONTEXT are reference-only, retrieval-indexed.

**Recalibrated acceptance gate — PASS (P6-137, ratified by Peter).** Cost-under-target PASS (Console authoritative). Zero-truncation PASS **on the real signal** — the canary's 55%/52% "truncation" was **gate mismeasurement**: the `truncated` flag fires on a 256KB *telemetry log-write* cap (`TRAJECTORY_RUNTIME_EVENT_MAX_BYTES`), smaller than the deliberate 120000-token window, so any window-filling session truncates its log record with **no agent context lost**; real bootstrap-brief truncation is clean. Retrieval-hit-rate PASS **on the real signal** — the index returns hits when queried (0.667); production recall is 0/day because automation/triage/heartbeat turns don't invoke search, it fires on genuine recall Q&A. `active-memory` auto pre-turn retrieval NOT pursued (capability cost for a metric that doesn't reflect a real problem). The **aggressive frozen-core slice** (verb tables → retrieval-only) + **dreaming-on** = deferred-Andreas.

---

# PART 7 — COST ARCHITECTURE

Spend is **heartbeat-metronome-dominated** (a beat costs even when `silent:true` — cognition runs regardless), Marie-dominant, Opus-heavy, amplified by context bloat and cache-misses (2h idle > 5-min prompt-cache TTL → each beat re-caches full context) — P6-68/P6-77/P6-85.

**The P6-85 levers (headline steady-state fix; ~$30/day → down):** Marie heartbeat 30m→2h daytime + none overnight (≈48→≈12 beats/day, LIVE hot-reload); `contextTokens` 200000→120000 (needs a restart to apply+compact); `compaction.model=sonnet-4-6`; `keepRecentTokens=40000`; overnight suppression + daily fresh-session.

**Model split (no per-turn routing exists on 2026.4.22 — a session runs ONE model):** the only axis is `model` (interactive) vs `heartbeat.model` (autonomous pulses). Final: Marie `model=opus-4-7` (Peter-facing + emails + booking reasoning), `pa.heartbeat.model=sonnet-4-6`; Charlotte `sonnet-4-6` (v1 triage; Opus at v2). **NEVER Opus for tests** (P6-51). Opus-4.8 is unavailable on the pinned build.

**cost-notify canary (P6-105/P6-107/P6-109):** `cost-notify.py` + LaunchAgent (180s) tails each agent's trajectory, groups turns into tasks by a 150s idle-gap, classifies chat vs heartbeat, sends a 💲 line with tokens + estimated $ + running daily total. **Local $ is calibrated-estimate only; the Anthropic Console is authoritative** (local overstated ~2.6–2.8×; the real-$ Cost-Report Admin API is built but gated on an Org account).

**The hard rule (P6-51, in force — CLAUDE.md + memory `no-unauthorized-test-spend`):** never spend materially on agent test-turns without explicit authorisation; default to inspection; a single short cheapest-model turn for a final F26; >~$5 or Opus/looping/browser-heavy needs authorisation first. The 6c.7 ~£120 Opus-test-loop day is the standing cautionary precedent. **Context-churn is a functional failure, not just cost** (P6-116): mid-task compaction lost deliverables and mis-reported — the cure is cutting the bloat that fills the window, not raising the cap.

---

# PART 8 — THE LAUNCHAGENT FLEET

| LaunchAgent | Cadence | Function | Status / evidence |
|---|---|---|---|
| `gateway` | RunAtLoad + KeepAlive | The OpenClaw gateway (gui/502) | LIVE (P6-43/P6-56) |
| `colima` | RunAtLoad | Auto-start Colima on boot (sandbox `mode:all` needs the daemon) | LIVE (P6-2) — **supersedes v6/STATE §2.4 "manual colima start"** |
| `colima-socket-watchdog` | 300s | Double-probe `docker info`, 15m cooldown, restart on genuine socket drop, notify | LIVE (P6-63) |
| `heartbeat-watchdog` | 900s | Stall-restart + fallback-storm alert + cost/context canary + real-blocker escalation + intra-day `:main` auto-reset | LIVE (P6-54/P6-58/P6-59/P6-79/P6-121) |
| `heartbeat-nightwindow` | 23:00 / 07:00 | Overnight heartbeat suppression, manual-pause precedence | LIVE (P6-86) |
| `session-freshstart` | 06:55 | Daily archive+clear of `:main` (never telegram:direct) + restart | LIVE (P6-86/P6-120) |
| `memory-health` | 23:55 | The memory-v2 canary | LIVE (P6-119) |
| `cost-notify` | 180s | Per-task 💲 token+$ push (calibrated est) | LIVE (P6-105/P6-107/P6-109) |
| `cred-save-watch` | 45s | Drains `.cred-queue/pending/` → host op-put → receipt + TG-delete | LIVE (P6-115); spin fixed (P6-126) |
| `reply-watcher` | 900s | Bounded Gmail poll of awaited threads → notify (interim outbound-bounce sweep) | LIVE notify-mode (P6-124/P6-129/P6-132) |
| `chat-bloat-reset` | 900s | Archive+clear `telegram:direct` when bloated+idle+cooldown → restart | LIVE, first cycle F26-confirmed (P6-129/P6-132) |
| `vault-backup` | 04:00 | Obsidian → Google Drive backup | LIVE |
| `overnight-verify` | one-shot self-removing | Verifies the night's suppression/reset evidence → notify → self-remove | fired 03-Jul, PASS 6/6 (P6-91/P6-96) |
| `security-audit`, `calendar-backup` | — | `.disabled-D24` (F44 7-day token) — re-enable = deferred-Andreas | disabled (P6-47/P6-61) |

**Cold-boot reality (P6-43):** FileVault-ON blocks auto-login; the gui/502 LaunchAgents don't start until Peter physically logs in (Screen-Sharing/keyboard) — a **manual** step ~1–2×/mo, accepted. KeepAlive cannot help a machine sitting at the FileVault screen. (This is the caveat STATE-OF-NATION's "survives power-cycle" line needs.)

---

# PART 9 — SUBSYSTEM REGISTRY (S-codes) + the S11 design

The S-series is the system/subsystem registry (S1…S12). This part is the canonical home of **S11**, the highest-risk previously-undesigned gap.

### S-code index (current)
- **S5** — cross-hardware DR dry-run (pending, post-6c).
- **S6** — architecture-vs-live-schema reconciliation (config-key obsolescence tracking on the pinned build).
- **S9** — the monitoring suite (the CoS's object-level job — memory-health/cost-notify/watchdog are its first instalments).
- **S11** — inter-agent rate-limiting / loop-protection (**designed below**).
- **S12** — reserved.
_(S1–S4, S7–S8, S10 predate Phase-6; see the frozen Phase-0 log.)_

## S11 — INTER-AGENT LOOP / STORM PROTECTION (the design)

**Why this is the one hole that can't ship unaddressed.** Once Andreas (CoS) can message the specialist agents, the fleet becomes a distributed system where **each message hop = one billed agent turn on Peter's metered Anthropic budget**. A loop is therefore not merely a correctness bug — it is a **runaway-cost event** of the exact class that burned ~£120 in a day (6c.7 Opus-loop). The [P6-59] watchdog-sawtooth is the live cautionary tale: a supervisor that can act on the system it watches must be unable to *harm* it (a broken watchdog starved pro's beat ~20h). **Loop-protection must be a hard mechanism, not a brief instruction** — model reticence is soft and probabilistic (the same lesson as the T5 "L1 wall").

**Trigger primitive (as-built):** inter-agent action today is `openclaw agent --agent <id> --message` (a billed gateway turn), `openclaw message send` (channel delivery), and the file-mailbox request/receipt pattern (`shared/cred-requests/` + a host-watcher). Agents currently coordinate mostly via shared files and **do not freely trigger each other** — so S11 governs the *future* A2A layer and MUST exist before it is enabled.

**Failure modes:** direct cycle A→B→A; indirect cycle A→B→C→A; fan-out storm (A messages all N; each replies to all N; O(N²)); amplification (one inbound user message ignites the fleet); silent drain (invisible until the cost canary trips hours later).

**Design principles (inherited from proven Phase-6 patterns):** choke-point over enumeration (gate-at-value); ride the durable-task ledger (every A2A exchange has a task_ref, inheriting proof-of-done); route through a **host-mediated mailbox** the host can meter and kill (like cred-save-watch) — NOT direct in-sandbox `openclaw agent` shell-outs (unmeterable, uncappable); reuse the cost-notify canary as the circuit-breaker signal; **fail closed, surface loud**.

**The mechanism — five layers (defence in depth):**

1. **L1 · Message envelope (provenance + depth).** Every A2A message carries: `conversation_id` (minted by the originating human-triggered turn, propagated unchanged); `hop` (incremented each hop; **hard max-hop / TTL, proposed 4** → drop+surface beyond); `origin` (the root human message — A2A with no human origin in the chain is refused, so no self-sustaining loops); `path` (ordered visited-agent list — if the next target is already in `path`, **cycle detected** → drop+surface, independent of hop-count); `task_ref` (the ledger row it rides — no task_ref → refused).
2. **L2 · Budgets (the storm cap), host-watcher-enforced before dispatch.** Per-conversation turn budget (**~8**); per-ordered-pair budget (**~2** — kills ping-pong under the hop cap); per-agent daily A2A budget (small fleet-wide ceiling, **~30/day** to start, tied to the cost figure); **fan-out cap** (an agent addresses at most **K=2** others per turn — prevents O(N²)).
3. **L3 · Cost circuit-breaker.** An A2A-attributable counter on the cost-notify canary; crossing the day's A2A threshold (tie to the ~£30 problem line) **suspends all A2A dispatch** (user-facing turns unaffected) and surfaces. Backstop for any L2 accounting bug.
4. **L4 · Kill-switch (host-side, single lever).** A sentinel file (`shared/a2a/HALT`) the host-watcher checks before every dispatch — presence = drain-and-drop all pending A2A. Flipped by the L3 breaker (auto), Peter (`touch`), or the reset-net on anomaly; recovery = remove sentinel + logged reason. Mirrors the deny-list fail-closed posture.
5. **L5 · Observability.** Every dispatch appends to a host-owned append-only `shared/a2a/ledger.jsonl` (conversation_id, hop, from, to, task_ref, allow/drop, reason). An `a2a-watch` sweep (or the heartbeat-watchdog) flags rate anomalies BEFORE the hard cap trips; a memory-health-style canary line reports A2A turns/top-conversation/drops.

**Topology notes:** Andreas is the hub (most A2A originates there) — give him a higher per-agent daily budget but the SAME per-conversation/cycle/fan-out caps (the hub is where a storm amplifies); spokes (Marie/Charlotte/Sophie) rarely initiate — a spoke *originating* a fan-out is the anomaly; no agent may message itself.

**Staging:** designed now (this doc); **built at the Andreas pairing (6d)** — the host-watcher, mailbox schema, L1–L5 — and it MUST land **before** Andreas's A2A orchestration is enabled. The sibling control is the [P6-128] hard code-level pulse send-gate (the outbound analogue; both are "hard mechanism, not brief rule").

**SURFACED to Peter (defaults, not decisions):** the numeric budgets (hop 4, per-conv 8, per-pair 2, fan-out 2, daily 30) are proposed starting points, to tune once real orchestration patterns are observed; and whether A2A rides the host-mailbox (recommended — meterable/killable) or a native `openclaw` A2A primitive should one appear on a future build.

---

# PART 10 — SESSION-ROUTINE AUTOPILOTS (SR-codes)

The SR-series is the automated background/close-time loops. SR-2 (calendar ICS-export → 1Password, the F44 mitigation precondition) exists. **SR-1** is designed here.

## SR-1 — DOC-AUTOPILOT / close-discipline loop (Andreas owns from 6d)

**What it automates** (today performed by the Claude-Code operator each close): the session lifecycle is **Stage 0 verify → recon → gated build → E2E test → close**; SR-1 owns the **close** and the **currency guarantees** — (1) the deviations log updated additive-in-place (new P6-N; appended updates; never delete/rewrite); (2) the session closure; (3) the next-session opener; (4) the carry-chain integrity check [P6-101]; (5) SHA-anchoring every changed file + recording the live `openclaw.json` sha; (6) committing the close deliverables in `_session-docs/` to the **docs repo (`openclaw-docs`, branch main)** and build artefacts (scripts, workspaces) in `~/.openclaw` to the **config repo (`openclaw-config`, branch main)** — two nested repos, distinct remotes ([P6-141] doc-currency fix, 6c.35); and at **milestone boundaries** (7) reconciling the snapshot docs from the P6 deltas + re-folding open items into `PRE-COS-BACKLOG.md`.

**The split — hard-scripted machinery vs agent judgement.** The reliability trap is an LLM doing a "mechanical audit" by reading; that is where drift creeps in (the per-opener carry-chain already drifted 6c.17/6c.19 under a human — [P6-101]). So SR-1 hard-scripts every mechanical check (a host `carry-chain-audit.py`, Andreas invokes, never re-implements in prose):
- **Gap/dangling audit** — assert `### P6-N` headers are contiguous with no gaps and every `[P6-N]` reference resolves (the `re.findall` audit — clean at every close, P6-1…138 gap-free at 6c.31).
- **Append-only assertion** — `git diff` the deviations log: no lines deleted/modified above the previous max P6-N.
- **SHA-anchor completeness** — every file in the session's `git diff --name-only` appears in the closure's SHA table (before/after sha256-12 + bytes); the recorded live `openclaw.json` sha equals the live file (A-7).
- **Carry-diff extraction** — parse the previous closure's "Carried →" list vs the new opener; emit the set difference for disposition.

Reserved for **agent judgement**: for each flagged carried thread, whether it was picked-up / completed / consciously dispositioned (parked-with-reason / superseded) — never silently dropped; authoring the closure narrative + the next opener's lead; the milestone reconciliation of which delta changes which snapshot section.

**Trigger/cadence:** event-driven on session-close (not a cron — a close needs the session's work as input); an extra reconciliation pass at milestone tags; an optional daily doc-currency canary that flags when a snapshot doc's "last reconciled" marker is > N sessions behind the deviations-log head.

**Guardrails:** never rewrite history (the append-only invariant is script-enforced, not trusted); commit `_session-docs/` at close but keep the **push** to origin a surfaced action; `openclaw.json` is out of SR-1's write scope (record its sha, never edit — F17); SR-1 records decisions, it does not make architectural/trust/scope calls. Full draft: the scratchpad SR-1 note folded into `BRIEF-DERIVATION-ANDREAS.md §Close-discipline`.

---

# PART 11 — THE DEFERRED-ANDREAS TRACK (design now, build 6d)

The Phase-6 throughlines converge on one owner. Andreas's remit (P6-49/P6-130 §C), all **designed** in v7 and **built** in 6d:
1. **Transactional last-mile** — CAPTCHA-solver (P6-39) + hosted-field cross-origin card-fill (P6-135, `pay-fill.sh` multi-target/per-iframe) + the `pay-fill.sh` PATH bug. Mechanisms proven up to the wall.
2. **The browser backend gap** — the Playwright-launched-Chromium swap for `refs=aria`/`efficient`; per-site recipes; `browser-dep-repair.sh` post-update guardrail.
3. **The hard code-level gates** — the pulse send-gate (P6-128) and the marie@ approval-token stop (the soft brief rules are coping-not-cure).
4. **Robust outbound-bounce/DSN sweep** — extend reply-watcher (P6-138; the HEARTBEAT rule is interim).
5. **The bloat/stick CURE** — the aggressive frozen-core brief slice (verb tables → retrieval-only, ≤30K) + dreaming-on + `:telegram:direct` compaction bounding (chat-bloat-reset is the proven backstop, not the cure).
6. **Platform stewardship** — infra-currency (propose→sandbox-test→migration-plan→surface, never auto-bump; an `openclaw update` wipes the P6-40 symlink + the P6-42 guard patch → run browser-dep-repair.sh after); re-enable the descoped launchd jobs (F64/F68/F70/D21-D24); the F44 org-owned Internal-consent OAuth flip; the dev/test cost-zone wiring; the per-task real-$ Admin-API path.
7. **The doc + orchestration halves of his own role** — SR-1 (PART 10) + S11 (PART 9), both of which must exist before he runs unattended.

The full open-item ledger lives in `PRE-COS-BACKLOG.md` (re-reconciled at this milestone boundary, [P6-101]).

---

# PART 12 — EVIDENCE INDEX (subsystem → primary P6 codes)

- **Security spine / host-mediated secrets:** P6-27, P6-83, P6-88, P6-99, P6-103, P6-110, P6-113, P6-114, P6-115, P6-126, P6-136.
- **2-tier browser:** P6-25, P6-34, P6-35, P6-37, P6-39, P6-41, P6-42, P6-44, P6-84, P6-99, P6-111, P6-129, P6-130, P6-135, P6-136.
- **Gate-at-value / payments:** P6-3, P6-42, P6-45, P6-73, P6-75, P6-84, P6-87, P6-99, P6-111, P6-124, P6-135, P6-136.
- **Durable-task ledger:** P6-69, P6-78, P6-81, P6-102, P6-132, P6-135, P6-138.
- **Chat-bloat-reset self-heal:** P6-54, P6-59, P6-77, P6-85, P6-86, P6-119, P6-120, P6-121, P6-125, P6-129, P6-132, P6-133, P6-137.
- **Memory-v2:** P6-36, P6-93, P6-97, P6-98, P6-116, P6-118, P6-119, P6-120, P6-126, P6-137.
- **Cost:** P6-28, P6-51, P6-53, P6-60, P6-68, P6-74, P6-77, P6-85, P6-86, P6-104–P6-109, P6-116.
- **Honesty/mirror:** P6-22, P6-62, P6-102, P6-103, P6-104, P6-125, P6-128, P6-134.
- **LaunchAgent fleet:** P6-2, P6-43, P6-54, P6-56, P6-59, P6-63, P6-79, P6-86, P6-91, P6-96, P6-105, P6-115, P6-119, P6-121, P6-124, P6-126, P6-129, P6-132.
- **OAuth/helpers/sandbox:** P6-1, P6-17, P6-21, P6-23, P6-24, P6-31, P6-32, P6-38, P6-81, P6-83, P6-89, P6-90, P6-95, P6-113.
- **S11 / SR-1 / Andreas remit:** P6-49, P6-59, P6-76, P6-101, P6-127, P6-128, P6-130.

_End of Master Architecture v7. Supersedes v6 additively; v6 retained as the design-intent tombstone. Reconcile again at the next milestone boundary (SR-1, Andreas-owned from 6d)._
