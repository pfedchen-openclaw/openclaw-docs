# SESSION 6c.19 → 6c.20 INTERSTITIAL — CLOSURE
_Closure for the Marie/Charlotte patch burst that landed between the 6c.19 close and the 6c.20 open (2026-07-03 evening). Record: [P6-102]…[P6-105] in the deviations log. Not a numbered build session — a Peter-directed hardening pass before he gives Marie her next tasks + runs the payments test._

## What triggered it
Peter reviewed the day's Telegram with Marie and flagged underperformance + cost: fabricated/disavowed "done" claims, narrow email formatting + a stale signature, screenshots that never arrived, ambiguous "is she working?" silence, a reply that waited for the 2h pulse, and a belief that spend had passed ~$120/day. Instruction: fix it across **all** agents, get costs precise + build per-task cost visibility, "add or push back," "keep working and do the restart yourself."

## What shipped (all live)
- **Behaviour ([P6-102]/[P6-103]) — Marie.** Receipt rule (no "done" without the tool-call receipt; SPA toast ≠ committed); the **mirror rule** (don't *dis-claim* real work on a flush/restricted-tool turn — the inverse failure that my P6-102 patch first caused); **working-state legibility** ("On it" on pickup, no silence mid-task, `HEARTBEAT_OK` only for a genuinely quiet beat); **append-only daily memory**; early CAPTCHA/adversarial abort. In AGENTS/SOUL/DECISIONS/MEMORY.
- **Behaviour ([P6-104]) — Charlotte.** The **cross-cutting** subset (mirror rule, working-state legibility, append-only memory) propagated to workspace-pro. Email/signature/screenshot mechanics stay **dormant** for pro (no send, no card in v1) — recorded for her v2 inheritance. commit `9355160`.
- **Email ([P6-103]).** `gmail_helper.py` now sends **multipart plain+HTML**, reflows paragraphs, renders `**bold**`/`- lists`, and auto-appends the canonical signature (**Marie** + marie@fedchenkov.com) on marie@ sends; drafts-as-Peter stay unsigned. F26-verified by rendering inside the pa container.
- **Media ([P6-104], corrects [P6-103] ⑤).** Ground truth from the send-history: the **`browser` tool's own screenshots deliver fine** (structured `mediaUrl`, host-absolute `~/.openclaw/media/browser/…`); the 3-Jul vanish was Marie **hand-typing** `MEDIA:/workspace/…` (a sandbox-absolute path the host gateway can't resolve). Fix: PA-PLAYBOOK + MEMORY route via the **`browser`-tool `screenshot` verb**; confirm the image appeared. commit `609dd1e`.
- **Cost-notify ([P6-105]).** Host poller `scripts/cost-notify.py` (LaunchAgent `ai.openclaw.cost-notify`, 180s, live) → per-task 💲 Telegram note with exact tokens + **estimated** $ (Console stays truth) + running daily total; chat-vs-pulse via `session.started.trigger`; cursor=now so no history spam — catches Peter's test tonight forward.
- **Model-routing ([P6-104]) — the cost lever, applied.** `agents.list[pa,pro].model` Opus-4-7 → **Sonnet-4-6** via new F17 stager `scripts/stage-model-sonnet.py` (other Opus agents provably untouched). **I ran the gateway restart** (GUI/Screen-Sharing terminal → D20 satisfied): healthy, pid 8799, 2h heartbeat + 120K context now live. openclaw.json `82a293d97aac`/10097B → `db78bba1aea8`/10101B, last-good synced (A-7). **Marie flipped too** (dominant cost signal, ~$1.54/Opus turn measured, reversible) — surfaced to Peter for a one-word veto; **pro flip also restores its documented Sonnet-for-triage design**.
- **1Password ([P6-103]).** `op-put --password-env` (store existing creds, secret-safe); Opera + Body 4 Life saved.

## Open threads carried into 6c.20 (nothing dropped)
- **Marie-model veto** — she's on Sonnet now; revert to Opus for real chats is one stager-inverse + restart if Peter prefers.
- **Reply-watcher activation (⑧)** — mechanism proven (`gog gmail search` + `openclaw agent … --deliver`); **blocked on dead host gog Gmail OAuth** (`invalid_grant`, both accounts; agents unaffected). Needs Peter's pick: re-auth gog (interactive consent) or a host poller on the agents' live cred.
- **All 6c.19 payment threads** (real-charge F26 on a plain merchant, autonomous login, Stripe/iframe strategy, battery re-run, T1 proof, optional slim, v7 capstone) — untouched by this interstitial, still live in the 6c.20 opener.

## Ledger at close
- openclaw.json / last-good: **`db78bba1aea8`/10101B**, 600/staff (A-7 clean). sanitised `3de319f97458`/10132B. Pin 2026.4.22.
- workspace-pa: AGENTS `3b967ca6f983` · PA-PLAYBOOK `45022cfe7876` · MEMORY `4c9f06fc13bb` · DECISIONS `5f20893ac60e` · SOUL `3c37d4515bdb`. commits `f9af477` (behaviour) + `609dd1e` (media). workspace-pro `9355160`.
- .openclaw repo `7d5c2e6`. Deviations log **`376a80668e44`/276491B**, **P6-1…105 gap-free, no dangling refs** ([P6-101] audit ✓).
- Cost discipline held: every analysis + verification by inspection / free backfill; **no billed agent test-turn** ([P6-51]).
