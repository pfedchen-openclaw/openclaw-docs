# PRE-COS-EXECUTION-PLAN — sequenced plan for 6c.10 + 6c.11 (→ 6d)
_Authored 2026-06-15 from the post-6c.9 direction-setting discussion with Peter. Companion to `PRE-COS-BACKLOG.md` (the inventory) — this is the **sequenced execution plan**. It refines the timing in `SESSION-6c.10-OPENER.md` and adds a **6c.11** to discharge the 6d build-start gate. It is an **input to ARCHITECTURE v7**, not v7 itself (v7 is a 6c.10 deliverable, so it is not pre-empted here). Decisions below are folded into the deviations log + v7 at 6c.10 close as formal P6-codes._

---

## Decisions locked in this discussion (2026-06-15)

- **D-PLAN-1 — Build order revised: Librarian moves up to right-after-Andreas.** Was "Librarian last" (6c.9 closure). Now: **Andreas (6d) → Librarian → Travel/Research → *(SDR held)* → Finance → Sophie.** Rationale: get Drive taxonomy owned sooner. **Precondition:** the Librarian's value is Drive *write* (move/rename/create folders) — blocked today (Marie has `drive.readonly` only). It is unblocked by the owned-GCP-project + Phase-G OAuth cluster, which D-PLAN-3 pulls into 6c.11. First Librarian mandate = design + propose taxonomy (read-only acceptable), then execute once write is wired. **Trust-surface note for v7:** the Librarian gets broad read across the whole document corpus (both Drives + Obsidian/Notion) — scope this deliberately in v7.
- **D-PLAN-2 — Hold 6c.10 ~3–4 days; checkpoint ~19 June.** [P6-64] autonomous-beat confirmation is intrinsically time-gated (needs multiple real delivered beats), and proactive delivery only became real at 6c.9 — so this is the first genuine practice window for the most important capability. Free (inspection only). Design/authoring (S11, S6, remit) is captured now so the wait is not idle. If beats are still silent on substantive digests by the checkpoint, the `dmPolicy`/visibility escalation ([P6-64]) takes priority over authoring v7.
- **D-PLAN-3 — Email infra + Marie's mailbox pulled into 6c.11 (earlier than 6d).** The Workspace stand-up + owned GCP/OAuth client + the F44 Internal-OAuth flip + Drive-write + Marie's mailbox cutover all land in 6c.11. The OAuth flip is the reliability prize (kills the 7-day refresh-token weekly breakage); Marie's mailbox rides the same Workspace stand-up.
- **D-PLAN-4 — Charlotte: no @fedchenkov.** Professional identity stays `peter.fedchenkov@soveren.io` → `@hylean.com` once the Hylean Workspace exists, driven by the **S10** ~8-month Soveren sunset (the forcing event — watch it). @fedchenkov is Peter's personal domain; using it for the professional assistant would conflate the two.
- **D-PLAN-5 — Agent-email convention: bare apex `marie@fedchenkov.com` (supersedes D24's `@team.` lock).** Final call (2026-06-15): agents live at the **apex** of the domain they belong to — `marie@fedchenkov.com`, and later `charlotte@hylean.com`. Drops the `team.` subdomain. Rationale: surname-confusion risk is negligible (recipients read the assistant's first name; the domain is just a domain), `team.fedchenkov.com` is awkward, and the apex convention generalises more cleanly across domains. The `@team.` separation bought little that matters — the real agent/human boundary is OAuth scope + the container, not the mail domain; the first-name convention already marks the agent class. (If high-volume automated sending ever creates a deliverability-reputation case, revisit a dedicated send-subdomain then, for that specific reason — not pre-committed now: Marie is low-volume, human-approved, draft-only/L1.) **This changes a locked D-code — record as a deviation (D24 superseded) at 6c.10 close.**

---

## Phase A — Practice window (now → ~19 June, ~zero effort, zero spend)
Passive, by inspection. The first real test of autonomous agent→Peter delivery.
- Watch [P6-64]: do real *beats* deliver? `openclaw system heartbeat last --json` → `status:"sent"`/`silent:false`; grep `gateway.log` for `using explicit accountId … channel=telegram`; confirm Peter receives Marie's 08:00 digest + any surfacing.
- Spot-check comms-hygiene across multiple real digests: no sign-off, no technical leakage ([P6-62]).
- Confirm Drive *surfaces* the no-upload limit rather than failing opaque ([P6-65]); `shared-docs/` in use.
- Note any new deviation → feeds v7 (cheaper now than retrofitting after Andreas inherits the patterns).
- **Checkpoint ~19 June:** if beats green → run 6c.10. If still silent on substantive beats → escalate the `dmPolicy`/`showAlerts`/visibility gates first.

---

## Phase B — Session 6c.10 (confirm + author v7) — run ~19 June
Refines `SESSION-6c.10-OPENER.md`. Stage 0 verify → Stages below → close.

- **Stage 1 — CONFIRM the 6c.9 fixes (the gate, zero spend).** [P6-64] autonomous delivery proven by a real delivered beat (not composed/inferred); comms-hygiene holds; Drive surfaces its limit. Do **not** proceed to authoring until this is real.
- **Stage 2 — [P6-60] wire the key zones.** `pro`→zone2 + route Claude-Code/test turns to zone4 (F17/F21 method: 1Password → `jq` by structure → display-immune sha-12/len verify → atomic 0600/staff → A-7 re-anchor → one cheap `pro` turn). zone3 dormant → Andreas at 6d.
- **Stage 3 — Author ARCHITECTURE v7 (the milestone, [P6-48]/[P6-49]).** Must include:
  - **[S11] inter-agent loop-protection design** — highest-risk gap; the [P6-59] sawtooth is the cautionary tale (a supervisor must not be able to harm what it watches). Counter location, ping-pong/fanout/burst detection, circuit-breaker, recovery. At least *designed* before a fan-out-capable agent goes live.
  - **[P6-49]** Andreas's engineering-steward remit: PROPOSE-not-EXECUTE, respects the version pin, owns the SR-1 doc autopilot, knows the P6-40/42 update-wipe.
  - **[S6]** reconcile MASTER-ARCH Part 5 vs the live 2026.4.22 schema **before** authoring Andreas's config off the (partly stale) template.
  - **[SR-1/D23]** doc-sync mechanism (push-on-change vs scheduled) + GitHub-publish decision.
  - **[P6-9/11/12/14]** lock voice/signature/wording conventions so Andreas's prose inherits them.
  - The **proactive-delivery contract** ([P6-64]): every agent→human capability is "live" only on a real delivered artefact.
  - **The revised agent build order (D-PLAN-1)** + the Librarian trust-surface scope.
- **Stage 4 — Reconcile + close.** STATE-OF-NATION / RUNBOOK / ARCHITECTURE from the 6c deltas; deviations log additive (formalise D-PLAN-1…4 as P6-codes); 6c.11 opener.

---

## Phase C — Session 6c.11 (6d build-start gate — the unblock cluster)
Discharges `PRE-COS-BACKLOG.md` §A.3. These are the things that must be true before **unattended** Andreas runs. None fit comfortably in 6c.10.

- **Email/OAuth cluster (D-PLAN-3):**
  - Stand up Google Workspace on `fedchenkov.com` (verify domain, MX, OAuth consent screen).
  - Owned GCP project + verified OAuth client → enables **Internal** OAuth posture (**[F44]**, ~30d grace — kills the 7-day refresh-token weekly breakage) **and** unblocks **Drive-write + Docs/Sheets writes** ([P6-32], blocked today because gog's published project has those APIs disabled).
  - Marie's mailbox cutover to `marie@fedchenkov.com` (D-PLAN-5) — move her off `pfedchen@gmail.com`. Still draft-only/L1; Peter approves sends.
- **[F64 + F68 + F70 / D21/D24] re-enable the descoped launchd jobs** — calendar-backup + security-audit are `.disabled-D24`, blocked on: F64 fail-fast on token-refresh hang; F68 launchd PATH gap (`node`/abs paths); F70 `op` unauthenticated in launchd (no `OP_SESSION`). Unattended automation Andreas would own is blocked until these clear. (Run launchd-touching steps from a Screen-Sharing GUI terminal — D20/P6-56.)
- **[F59] rotate `GOG_KEYRING_PASSWORD`** — plaintext-exposed, Peter-deferred; land before unattended running.
- **[P6-40/42] encode `browser-dep-repair.sh`** as an after-every-`openclaw update` step → into Andreas's guardrails.
- **[P6-45] payment guardrail** as a design constraint — gate value at the card issuer, not in the agent.
- **Close →** the fresh **6d opener** (Andreas build kickoff), iff Phase-A confirmed + this cluster cleared.

---

## Phase D — 6d and beyond (recorded; own plans)
- **6d — Andreas (CoS).** Built on the proven pair; AGENTS/TOOLS derived from the *current* Marie/Charlotte canon ([P6-26]). Inherits §B standing constraints + §C build-content (S9 suite, doc autopilot, maintenance cadences). F2/F26 pairing proven through a real turn.
- **Post-6d, the "before other agents" period** = Andreas beds in and takes ownership of the S9 monitoring/automation suite, the SR-1 doc autopilot, the §D maintenance cadences, and the descoped launchd jobs. No new build until he's proven absorbing that load.
- **Then, in order (D-PLAN-1):** Librarian → Travel/Research → *(SDR held — Hylean active-sales trigger)* → Finance → Sophie.

---

## "Fully operational" — the end-state definition
Not an empty deviations log (impossible by design). Three conditions:
1. **Zero open *gating* codes** (§A) — DONE/DECIDED. Achievable after 6c.11 + 6d.
2. **Every deferred capability built or explicitly consumer-gated** (§E) — nothing forgotten; each sits behind a named trigger.
3. **The CoS owns the standing constraints (§B) + maintenance cadences (§D)** — they become *enforced/tracked*, not *open work*. This is the structural shift that makes the system self-sustaining.

Reached at **end of 6d**, once Andreas is proven through a real turn and owns the S9/doc/maintenance loops. Further agents are additive expansion, not completion work.

---

## Watch-items / carry-forward
[P6-64] autonomous beat (lead) · [P6-46] `agent:pa:main` 80%-context reset · [P6-62] Sonnet-persona-turn test verb · browser/Sheets selector hang ([P6-59]) · memory-pressure morning window (relevant once Andreas adds a 3rd heavy heartbeat) · 2026.6.6 update (pinned — surface before bump, run `browser-dep-repair.sh` after) · S10 Soveren 8-month sunset (Charlotte's forcing event).
