# CAPABILITIES ROADMAP & ARCHITECTURE DECISION — path from 6c.1 to 6d
_Authored 2026-06-06, after 6c.1. **Revised 2026-06-12 (P6-33): CoS-first.** Governing plan for proving the **Peter-facing core pair** — Marie (PA) and Charlotte (ProA) — fully up and operational, with all lessons baked in, **before** building CoS (Andreas, 6d). The multi-human trust surface (non-owner Telegram correspondence; Sophie, family coordinator) is **deferred past 6d** — piloted via Marie first, stood up only once the core is proven. Single forward source of truth._

**Supersedes / reconciles:**
- The 6c.1 closure's "next = calendar (6c.2)" ordering — **the capable image now jumps ahead of calendar** (§3).
- `SESSION-6c2-CALENDAR-HELPER-OPENER.md` — **superseded**; calendar moves into 6c.3. Its durable-vs-Testing OAuth decision is carried into 6c.3.
- The old **6d opener** generated at 6c close — **superseded**; the 6d opener is regenerated fresh at the pre-6d consolidation (6c.10).
- Supersedes the **sequencing** in `EXTERNAL-ACTUATION-LAYER.md` (predates the image-first reorder); its messenger/voice analysis stays valid. This doc owns the pre-6d plan.

---

## 1. Strategy — CoS-first (revised P6-33)
Run Marie + Charlotte in real daily use for ~a week to prove the Peter-facing core, then build **CoS (Andreas, 6d)** — the most important agent — on that proven pair. One day of practice surfaced more than theorising (egress, the empty-dir bug, stale-mount, the consumption-wiring gap, the document gap, the container question). **The multi-human trust surface is deferred past 6d:** non-owner Telegram contact-correspondence and **Sophie** (family coordinator) both introduce a second class of human the agents must interact with safely, and that surface is best piloted via Marie on-demand and stood up only once the single-operator core is proven. Sophie, when built, still inherits everything that works on the pair (P6-26) — cheaply and at low risk. This drops the pre-6d **trio shake-out**.

## 2. Architecture decision (ADR) — keep the container, fix the image
**Decision:** keep per-agent container isolation; **replace the clean slim image with a capable, maintained image**; reserve a minimal-privilege **host-bridge** only for the few genuinely macOS-bound capabilities; keep secrets outside the container; gate every external send/submit/call by the autonomy ladder. **Do NOT run agents directly on the host.**

**Reasoning:**
- *Steelman for host:* the cloud model already reads everything an agent processes, so the container's marginal value is not hiding the email being read — it is stopping a *compromised* agent from reaching everything *adjacent* (full 1Password vault, every mailbox, SSH keys, passport/immigration + financial files, the other agents' creds). If exposure were low and that adjacency risk acceptable, host-as-least-privilege-user would be simpler with native tooling.
- *Why keep the wall:* (1) the agents' core job is ingesting untrusted content — the highest prompt-injection exposure a design can have, and it's the daily workload; the container bounds what an injected agent can *reach*. (2) Capability/autonomy is rising (sign, send, message contacts, call) — every rung makes a compromise worse, so isolation matters **more** as we scale. (3) A container is a hard, auditable boundary; macOS least-privilege is soft and error-prone, and a shared host loses per-agent isolation.
- *The pain is the implementation, not the wall:* hand-rolling a zero-dependency helper per capability on a clean slim image is the fragile scaffolding to stop building. Fix = a capable image with tooling native-in-container. Swapping it is a supported config change (`sandbox.docker.image`), not a hack.
- *Longevity flips in our favour:* the durable, portable asset becomes a standard Docker image of standard tools — runs under plain Docker anywhere if OpenClaw's sandbox feature ever rotted.

**Capable image contents:** `google-api-python-client` + `google-auth-oauthlib`, `python-docx`, `openpyxl`, **LibreOffice headless** + fonts/locale, **Playwright + headless Chromium**, retained curl/jq/openssl/python3-pip. Shared by **all** agents (global skill scope) — foundational, not per-agent.

**Hard watch-point:** LibreOffice and Chromium **write** (profile dirs, /tmp, user-data-dir), unlike the stdlib helper, and will fail under `readOnlyRoot:true`. The image session must add writable scratch (tmpfs `/tmp`, a writable HOME) while keeping the rootfs read-only — verify how `sandbox.docker` exposes tmpfs/mounts/env on the pinned build (A-12) before configuring.

**Host-bridge (post-6d only):** true MS-Office fidelity, iMessage, telephony/voice — a tiny audited host daemon, one narrow API each, agent stays contained. Not needed pre-6d (LibreOffice covers the Word-signing case).

## 2a. New definition of "done" (F26 — the 6c.1 retro lesson)
A capability is **not live until it has been exercised end-to-end through the actual sandbox runtime by a real agent turn** — never on bootstrap/cognition alone, never on a one-shot mirror alone. When a security posture is set, it is tested immediately against each function it could break. This is the exit gate for every session. (Root cause of the 6a gap: "boots and talks" mistaken for "can do the job"; gog-on-host vs clean-container never reconciled.)

## 3. Sequence to 6d
Each is a **single-purpose session**, opened fresh with the prior closure, **closed only when end-to-end tested through a real agent turn**, budget reserved for clean closure (~63% context). Groupings may compress once the image lands.

| # | Session | Scope | Exit (tested through a real turn) |
|---|---------|-------|-----------------------------------|
| 6c.2 ✓ | **Capable image** (foundational) | Build the image; point `sandbox.docker.image` at it for defaults + per-agent blocks (P6-24 sync); writable scratch under readOnlyRoot; recreate pa+pro after build (P6-23) | **DONE.** Both agents on the new image; Google API call, Playwright page load, docx→pdf convert, xlsx read all succeed in a real turn; A-7 clean; Gmail regression-checked |
| 6c.3 ✓ | **Google APIs** | Calendar + Drive + Docs/Sheets via the standard client; durable OAuth (re-consent on the published client with calendar/drive/docs scope — F44); SR-2 ICS export to 1Password **before** calendar write; resolves P6-4. Optional: migrate Gmail off the stdlib helper | **DONE (P6-32).** Marie reads/proposes calendar + reads/writes a Drive file live; F46 discharged for the live path |
| 6c.4 | **Web** — **wire both pa + pro** | Read + interactive headless browser (Playwright) for **Marie and Charlotte**; bound `browser_helper.py`/pattern (`--no-sandbox --disable-dev-shm-usage`, P6-31); reads free, submits L1 prepare-and-surface; TOOLS.md updated for **both** agents | **Both agents** can read a JS-heavy link; Marie operates a real portal (the Mews check-in) to a surfaced-for-approval submit, end-to-end |
| 6c.5 | **Local documents** — **wire both pa + pro** | docx/xlsx open+edit; export PDF; **email-attachment** support added — for **Marie and Charlotte**. (Signature-PNG insertion **deferred** — build later against real examples; see Deferred backlog.) | **Both agents:** open a .docx, edit, produce a PDF, draft an email with it attached — surfaced for approval |
| 6c.7 | **Ops & reliability pass** (now also absorbs chat-surface hygiene) | F28/P6-2 cold-reboot proof (+ F45 LaunchAgent auto-start) — **now proven to bite first-contact** (Marie's 5 Jun errors were Colima down); **scheduling reliability (P6-29):** diagnose the durable-timer gap, back fixed-time digests + one-off reminders with launchd/cron, **re-test Marie's 08:00 digest** end-to-end post-egress-fix; improve the generic first-contact error message; uptime/health alerting; **host-egress hardening (P6-35 — decided at 6c.4 to land here): DROP container→`192.168.5.2` host-loopback (gateway control-plane + SSH reachable via lima `host.lima.internal`) while preserving DNS (192.168.5.1) + public internet; verify persistence across colima restarts**; **API cost/budget alerting (Opus runs 24/7 — watch lightweight-heartbeat spend)**; **backup/recovery confirm — name the assets: `openclaw.json` config, each agent's `MEMORY.md`, the 1Password "OpenClaw" vault, and agent-written data (SR-2 ICS exports, Drive files)**; **chat-surface hygiene (folded in from the dissolved 6c.6 — P6-27 + P6-30):** suppress the tool-call trace **and** bootstrap/internal-step narration (success-only, must preserve P6-22 error surfacing); emoji calibration; Charlotte avatar `/setuserpic` + `agents set-identity`; apply the safe voice/signature batch (P6-11/12/14; P6-9 held) | Stack survives a cold reboot unattended; an 08:00 digest and a one-off reminder both fire on time; a simulated outage pings Peter; a cost-threshold alert fires; backup/restore of the named assets verified; the sandbox can no longer reach the host gateway/SSH at `192.168.5.2` (P6-35) while DNS + public egress still work; no tool-trace or internal narration leaks into the chat; shas re-anchored |
| — | **→ Practice week** | Marie + Charlotte in real daily use; issues feed back as deviations | n/a (elapsed time, low active effort) |
| 6c.8 | **@fedchenkov.com email** (slimmed) | Google Workspace on the domain (verify, MX, OAuth). **`sophie@` dropped** (Sophie deferred). **Marie's `marie@fedchenkov.com` mailbox is a pre-or-post-6d decision** — driver: move Marie off Peter's personal Gmail (`pfedchen@gmail.com`); decide whether to do it before 6d or after. Pulls P6-8/D24 forward only if taken pre-6d | If taken: Marie sends/receives from `marie@…` end-to-end |
| — | **Correspondence pattern-review gate (pre-6d, B7/P6-33)** | Review-and-fix the agents' real correspondence (drafts, tone, signature, error-surfacing, autonomy gating) and **lock the patterns** — Andreas inherits these at 6d (P6-26). Not a build session; a quality gate over the practice-week output | Correspondence patterns reviewed, fixed, and frozen as the canonical patterns CoS will inherit |
| 6c.10 | **Pre-6d consolidation** | **Reconcile the canonical snapshot docs from the deviations deltas** — STATE-OF-NATION, RUNBOOK (Claude Code workflow, gmail helper, calendar-via-direct-API, autonomy/permission model, git docs-repo, quiesce-during-builds), ARCHITECTURE (→v7 only if structural ADRs warrant), and fold crystallised lessons into PHASE-0-LESSONS/TIER-1-KEY-PATTERNS; commit. Hand the ongoing reconciliation cadence to Andreas (CoS: operator docs + maintenance). Generate the fresh 6d opener | Canonical snapshot docs reflect reality and are committed; clean 6d opener generated |
| 6d | **CoS (Andreas)** | Build on the proven **pair**; AGENTS/TOOLS derived from current canonical patterns (P6-26); inherits all shared capabilities, F26 def-of-done, P6-23/24/25, and the reviewed correspondence patterns | (its own plan) |
| post-6d | **Sophie** (family coordinator) — **deferred** | Stood up once the family surface is ready; derived from the then-current Marie/Charlotte/Andreas patterns (P6-26); her mailbox, family calendar, Telegram opt-in | (own plan; see Deferred backlog + §3a build order) |

## 3a. Build order — agents (revised P6-33, CoS-first)
The pre-6d **trio shake-out is removed.** Order:
1. **Marie** (PA) — ✓ live.
2. **Charlotte** (ProA) — ✓ live.
3. **Andreas** (CoS, 6d) — **next**, on the proven pair.
4. **Travel / Research** — after CoS.
5. **SDR** — at Hylean active-sales (driven by need, not the build clock).
6. **Finance** — after Drive / sensitive-data handling is proven.
7. **Sophie** (family coordinator) — once the family surface is ready (multi-human trust piloted via Marie first).
8. **Librarian** — last.

## 3b. Per-agent capability matrix (6c.4 / 6c.5 wire both; confirm by role)
| Capability | Marie (pa) | Charlotte (pro) |
|---|---|---|
| Web (read + interactive browser) | yes | yes |
| Local documents (docx/xlsx/PDF, attachments) | yes | yes |
| Gmail | yes (`pfedchen@gmail.com`) | yes (Soveren account — different mailbox) |
| Calendar | **owns** | **limited** |
| Drive / Docs | confirm by role | confirm by role |

## 3c. Deferred / on-demand capabilities (backlog)
Recorded, intentionally out of the pre-6d path; build on demand or post-6d:
- **Non-owner Telegram contact-correspondence** (the dissolved 6c.6) — opt-in deep link, disclosed-assistant greeting, contact-messages-are-never-instructions, L1-gated scheduling. Multi-human trust surface; pilot via Marie on demand, stand up post-6d. Chat-surface hygiene (P6-27 + P6-30) is **not** deferred — it folds into 6c.7.
- **Signature-PNG insertion** (pulled from 6c.5) — low-frequency; build later against real document examples, prepare-and-surface.

## 4. Locked decisions (this thread)
1. **Signature = PNG insert + PDF** (not e-signature tooling); prepare-and-surface. **— Revised P6-33: signature insertion deferred** out of 6c.5 (build later against real examples); docx/xlsx + PDF + attachments stay in 6c.5.
2. **Office/Docs/PDF/browser/Google libs = shared** by all agents (in the image).
3. **WhatsApp deferred post-6d.** Real value recorded: Marie messaging Victoria, Inna, a nanny group with Robyn for Sophie.
4. **Telegram opt-in** — compliant deep-link; non-owner-inbound routing to verify; messages-not-instructions; scheduling L1-gated. **— Revised P6-33: dissolved 6c.6 and deferred** (multi-human trust surface, post-6d / on-demand via Marie; see Deferred backlog §3c). Chat-surface hygiene (P6-27 + P6-30) folds into 6c.7.
5. **@fedchenkov.com at 6c.8**, after the pair's practice week. **— Revised P6-33: `sophie@` dropped** (Sophie deferred); **Marie's `marie@fedchenkov.com` mailbox is now a pre-or-post-6d decision** (driver: move Marie off Peter's personal Gmail).
6. **Sophie deferred past 6d** (revised P6-33; was "before Andreas at 6c.9"). CoS-first: build Andreas on the proven Marie+Charlotte pair; stand up Sophie once the family surface is ready, deriving from the then-current canonical patterns (P6-26). See build order §3a.
7. **Sophie & Andreas inherit the CURRENT Marie/Charlotte AGENTS.md/TOOLS.md patterns** (P6-26) — never a pre-6c.1 template. 6c.10 snapshots the canonical patterns.
8. **6d opener regenerated fresh** at 6c.10; old opener superseded; CoS re-derived from practice learnings.
9. **Avatar captured:** P6-20 addendum in the log; `AVATAR-TEMPLATE.md` canonical (supersedes P6-7 pixel-art); Charlotte LOCKED, `/setuserpic`+`set-identity` pending (6c.7); Marie re-gen deferred.
10. **Marie & Charlotte → Opus** (P6-28): Sonnet judged inadequate in real use; activate the P6-15 v2 posture now via a hot-reload model edit to **`claude-opus-4-8`** (apply at the 6c.2 open, gated; A-12-verify the pinned build accepts it). The agent model is an **API string billed on the OpenClaw API key** — independent of Peter's Max subscription / Claude Code tier; web/Code "Max/Extra" labels don't apply. Per-task "Sonnet for bulk scan" deferred (per-agent model only); cost on lightweight heartbeats to watch.
11. **Scheduling reliability** (P6-29): back fixed-time digests + one-off reminders with launchd/cron, not the heartbeat-as-timer; re-test Marie's 08:00 digest post-egress-fix; agents must not promise a timed action without a durable scheduler and must surface scheduling failures (P6-22). Resolved at 6c.7.

## 5. Deviations triage (before any new agent)
- **Before the practice week (reliability, all in 6c.7):** F28/P6-2 cold-reboot proof; F45 LaunchAgent auto-start; uptime/health alerting; backup/recovery confirm.
- **In the sessions:** Calendar (P6-1) + SR-2; Drive scope (P6-4); email attachments.
- **Decision taken:** apply the safe PENDING-BATCH-APPLY items (P6-11/12/14) now in 6c.7 — CoS, who would have applied them, is deferred and Marie's drafts are in daily use. **P6-9** (Marie may draft Hylean) held — scope change.
- **Defer:** SR-1 (operator-docs/CoS sync — 6d); P6-13 (contact→timezone — at scheduling/messengers); P6-20 Marie avatar re-gen (batch later); F70 (1Password unattended read — payments); messengers/voice (post-6d, see `EXTERNAL-ACTUATION-LAYER.md`).

## 6. Post-6d (recorded, not in scope now)
Messengers at scale + voice — full design/constraints in `EXTERNAL-ACTUATION-LAYER.md` / P6-25. Higher-autonomy phase, after CoS.

## 7. Estimate to 6d (revised P6-33)
**Path to 6d:** 6c.2 ✓ → 6c.3 ✓ → 6c.4 → 6c.5 → 6c.7 → practice week → correspondence pattern-review gate → 6c.8 (slimmed) → 6c.10 → 6d. **Removed:** 6c.6 (dissolved; hygiene → 6c.7), 6c.9 Sophie (deferred post-6d), the trio shake-out (−2 build sessions).

Based on 6c.1 pace; integration surprises can extend any session; the capable image cuts per-capability friction so 6c.4–6c.5 run lighter than 6c.1. External waits (DNS/Workspace in 6c.8; a real reboot in 6c.7) add wall-clock that isn't active work.

| Session | Active hours |
|---|---|
| 6c.2 capable image | ✓ done |
| 6c.3 Google APIs | ✓ done |
| 6c.4 web/browser | 2–3 (browsers are fiddly) |
| 6c.5 docs (signature deferred) | 2–3 |
| 6c.7 ops & reliability (+ chat hygiene, cost, backup) | 2–3 |
| 6c.8 email domain (slimmed) | 2–4 (Workspace/DNS waits) |
| 6c.10 consolidation + doc reconcile + 6d opener | 1.5–2.5 |

- **Build sessions to 6d:** 7 (6c.2–6c.10 minus 6c.6/6c.9), 2 already done; **5 remaining** before 6d, plus the (non-build) correspondence pattern-review gate.
- **Active work to 6d:** ≈ **13.5–21.5 hours** total (mid ~17); ≈ **9.5–15.5 remaining** (6c.4 onward).
- **Calendar time:** ≈ **2–3 weeks**, dominated by the ~1-week practice period, not by build hours (trio shake-out removed).

## 8. Current state (anchors, as of 6c.1 close)
- `openclaw.json` live sha `bfb3375d17f9b9b7` (bridge + per-agent sandbox blocks); gateway pid 56991 *as of close* (changes on restart — not canonical).
- Gmail helper `~/.openclaw/agent-creds/gmail_helper.py`, sha12 `5fd20fd379a7`, 644.
- Creds `agent-creds/{pfedchen,soveren}-gmail.json`, 600, durable.
- Marie: AGENTS `2966a8599e13`, TOOLS `d4001c96749d`. Charlotte: AGENTS `bd0fa295eee0`, TOOLS `69876f4d110d`.
- Containers: `scope:agent`, `net=bridge`, `readOnlyRoot:true`, binds = workspace + `/opt/gmail_helper.py` + `/creds/google.json`; lazy respawn. (Image swaps at 6c.2 → these blocks + the config sha change.)
