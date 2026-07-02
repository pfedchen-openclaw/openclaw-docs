# SESSION 6c.17 — Overnight-test verdict readout + acceptance-gate scoring (+ v7 capstone if room) ([P6-76])
_Opener · generated at 6c.16 close (2026-07-02). 6c.16 scheduled the overnight first-test as a self-removing 07:15 morning verifier that pushes a verdict to Peter's Telegram ([P6-91]), and authored + opened the **acceptance gate**: criteria locked (HYBRID window · STRICT-100% bar · battery AUTHORISED ~$5), scorecard + seed-prompts live. Read the 6c.16 closure + `ACCEPTANCE-GATE.md` + `ACCEPTANCE-GATE-SCORECARD.md` + `JUDGEMENT-DOCTRINE.md` before acting._

## Context / can this run over SSH?
Mostly SSH-safe (log reads, scorecard scoring by inspection, doc reconciliation). GUI/Aqua only if a gateway restart / LaunchAgent change is needed (e.g. the overnight verifier failed and needs a re-run, or a scorecard remediation touches config). Billed agent turns only for acceptance-gate tasks Peter fires — inspection-first, [P6-51] applies.

## Stage 0 — Ledger (no mutating work until GREEN)
- **A-7:** live == last-good == **`8a6e2a19d9d5`/10096B**, 600/staff. Pin **2026.4.22** (do NOT bump). Both sandboxes `capable-2026-06`. (Bash deny blocks `sha256sum`/`cat`/`jq`/`stat`/`ls` on `openclaw.json` — use `shasum -a 256` + `wc -c`.)
- **Deviations currency:** P6-91 present; log was **`78a6053c71d7`** at 6c.16 close — re-anchor.
- **WS-4 still live ([P6-90]):** cred `43353284e50f`/416B; `gmail_helper.py` `5bc5d7b73a4e`; `SEND_IDENTITIES` floor @156; pa mounts `/creds/marie-send.json`; docs `8fa873cd295c`/`a6aba8f23e51`.
- **★ Overnight verdict readout (the 6c.16 deferral, now due):** read **`logs/overnight-verify.log`** for the 07:15 PASS/PARTIAL/FAIL verdict of the night-of-02→03 run, and confirm it reached Peter's Telegram. The one-shot should have **self-removed** — verify `launchctl list | grep overnight-verify` is **empty** and `~/Library/LaunchAgents/ai.openclaw.overnight-verify.plist` is **gone**. If the verdict was PARTIAL/FAIL, diagnose from the nightwindow/freshstart logs + markers (bootstrap state / calendar-interval / sleep-wake) and fix — GUI if a restart/bootstrap is needed. If the verifier itself didn't fire (empty log), check its `.launchd.err.log`.
- **1Password green:** `op whoami` = `openclaw-agents` SA (`6LYBZQU6…`).

## Stage 1 — the work
1. **★ Advance the acceptance gate (lead).** Update `ACCEPTANCE-GATE-SCORECARD.md` from whatever battery/organic tasks Peter fired since 6c.16 — score each by **inspection of the real artefact** (Sent item, TASKS.md proof row, calendar entry, 1Password item, tracker), never the agent's self-report. **STRICT-100%:** any `~`/`F` on any dimension, or any fake-done/discretion-breach cardinal → remediate (brief/memory fix) and **re-run** that task through the real Telegram path. Keep the running verdict + cost tally current. The gate decides PASS only when every battery task + a fair organic sample all-pass. If Peter hasn't fired tasks yet, prompt him with the seed list and score live.
2. **v7 capstone / reconciliation (if room / if the gate stalls on Peter's pace).** The other pre-Andreas milestone (roadmap §6c.10 consolidation): fold P6-77…P6-91 deltas into STATE-OF-NATION / RUNBOOK / ARCHITECTURE(→v7 only if structural ADRs warrant); fix the stale `AGENTS.md:16` "Drive is READ-ONLY … no upload yet" vs live drive.file write ([P6-83]); crystallise lessons into PHASE-0-LESSONS / TIER-1-KEY-PATTERNS.

## Stage 5 — Verify (F26, strict cost) + Stage 6 — Close
Acceptance-gate tasks run through the **real Telegram→gateway→sandbox** path (embedded `openclaw agent` mis-tests — [P6-81]); cheapest-sufficient model, ~$5 battery cap, stop-and-surface if trending over. Close: deviations additive; `SESSION-6c.17-CLOSURE.md`; `SESSION-6c.18-OPENER.md`. Re-anchor shas; commit `_session-docs/`.

## Safety rails (whole session)
Reversible build runs without prompting; **surface irreversible/destructive:** real sends beyond the gated prepare-and-surface flow, OAuth/Workspace/GCP/billing, version-pin/D1, 1Password writes/deletes, depairing, reboots. **Cost ([P6-51]):** inspection-first; gate tasks are Peter-fired real turns within the ~$5 auth. **Secrets ([F21/F51]):** never read secret values; `agent-creds/**` + `openclaw.json` by structure / display-immune / script-driven staged edits. GUI/Aqua for consent/restart/LaunchAgent/`op`-admin (D20). Stop at ~63% context for a clean close.

## Carry-forward (deferred, not lost)
- **v7 capstone** + **acceptance-gate completion** = the two remaining pre-Andreas milestones; only when the gate PASSES do we author the 6d Andreas opener.
- Andreas backlog: org-owned OAuth app (Internal consent) to replace pfedchen-owned `openclaw-pf` (PRE-COS-BACKLOG); per-task cost tracker ([P6-85] item#1).
- DMARC `p=none` → quarantine/reject once reputation builds. Travel = POST-6d (roadmap §3a). Model/framework currency ([P6-74]) → Andreas. 2026.4.22 pinned.
