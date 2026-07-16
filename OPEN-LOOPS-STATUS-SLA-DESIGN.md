# Open-loops status-SLA — design (6c.37, [P6-156]) — for Peter's ratification

**Problem it kills.** Marie goes silent on a live task past any tolerable window — the 16 Jul Aviva case: chat dropped ~11:04, no status until Peter poked at 12:14 (**68-min silence**). Twenty-plus sessions of behavioural prose ([P6-149]: "never end a turn mid-task silently", "flag a drop the moment you see it") were written **24h before** she violated every line. Prose can't fix a structural gap ([P6-116] mandate). This makes "never go silent on an active task" **host-enforced**, not intended.

## Shape (reuses the proven reply-watcher pattern)
1. **`workspace-pa/memory/open-loops.json`** — a compaction-proof register. On disk ⇒ survives every compaction/reset (the thing that eats working context). Marie **opens** a loop the moment she starts an active task (the write is the acknowledgement, exactly like `TASKS.md`), **advances `last_status_to_peter_at`** every time she sends Peter a status, and **closes** it (`status=done`) when finished. One row = `{id, what, who_waiting, opened_at, last_status_to_peter_at, next_action, status, sla_minutes?}`.
2. **`scripts/open-loops-watch.sh`** — a host watcher (launchd, every 300s). Reads the file; for any loop `active`/`waiting-external` whose silence exceeds its SLA, it fires **once per SLA window** (re-arms only when Marie sends a fresh status or a full SLA re-elapses). It fires **regardless of Marie's context state** — even while she's mid-compaction-thrash or her turn has ended. That is the whole point: the enforcement does not depend on the agent that's failing.

**Mode ladder (identical to reply-watcher, which you already tuned):**
- `dryrun` *(built, current)* — evaluate + log only, £0. Verifiable before any live flip.
- `notify` — **free** Telegram to *you*: "⏳ No status from me on \"X\" for Nm — …". You learn she's quiet even if she's stuck. Not a billed turn.
- `poke` — **billed**, capped/day: wakes *Marie* to send you a status herself, then update the file.

**Built + verified this session (all £0, inert):** the file + watcher + staged plist. Dryrun tests pass — SLA gating (active 45m fires, fresh/`waiting-external`/`done` correctly excluded), dedup (same status-ts suppressed), re-arm (advanced status-ts re-fires). Nothing enforces until you pick a mode.

## Decisions I need from you (design-first per your steer)
1. **Enforcement mode.** Recommend **`notify` default + capped `poke` for `active` loops** — mirrors your reply-watcher choice (free signal to you always; billed self-surface only for the sharpest case). Alternatives: notify-only (never spend), or poke-first (Marie always surfaces herself).
2. **SLA thresholds.** Recommend **active = 45 min**, **waiting-external = 4 h** (16 Jul silence was 68 min; 45 gives margin without nagging). Tune either.
3. **Scope.** Start on **Marie (pa)** only; the file+watcher generalise to Charlotte/Sophie/Andreas unchanged when you want.

## Residual (honest)
Enforcement depends on the loop being **registered**. If Marie never opens a loop, the watcher can't guard it — so the brief ties loop-open to the existing `TASKS.md` "the write is the acknowledgement" reflex, and the reply-watcher (awaited Gmail) + heartbeat still cover external-reply silence independently. The durable belt-and-braces (engine-level keep-alive so a live chat never dies at the turn boundary) remains an **Andreas root** ([P6-153] root #2) — this is the harm-reducer that buys reliability until then.
