# SESSION 6c.1 — Sandbox Egress + Gmail Access — CLOSURE
_2026-06-06. Resolves **P6-21** (highest priority); applies **P6-22**. Both live agents (Marie/`pa`, Charlotte/`pro`) can now act externally on Gmail from inside the `mode:all` sandbox. **6d (CoS) is unblocked.**_

## Outcome
Marie pulls her personal inbox (`pfedchen@gmail.com`) and archives live; Charlotte pulls the Soveren inbox (`peter.fedchenkov@soveren.io`) and auto-archives spam — both end-to-end from inside their containers. **P6-21 RESOLVED, P6-22 APPLIED.** Calendar I/O (P6-1) deliberately deferred to **6c.2** on these same rails.

## What was done (arc)
- **Diagnosis (read-only).** `sandbox.docker.network` was `"none"` → blocks all container egress. Image `openclaw-sandbox:bookworm-slim` is clean (no `gog` binary, no keyring mount); only the workspace is bound. No per-tool host/sandbox routing exists (`sandbox.mode` enum = off/non-main/all); `allowHosts` is media-SSRF policy, not an egress allowlist. The network validator (`getBlockedNetworkModeReason`) blocks only `"host"` + container-namespace joins — `none` and `bridge` are valid.
- **Decision — Strategy 2 (Peter's call).** Keep `mode:all` (isolation stays on the hostile-content loop), flip `network` **none→bridge** (open egress only), retire gog from the agent path for a direct stdlib Gmail helper. Strategy 1 (`mode:non-main`, host main loop) rejected — it un-sandboxes the inbox-reading loop. Under bridge, `secrets.json`/auth-profiles remain outside the container, so an injected agent's exfil dead-ends — that boundary is the point.
- **Built + tested.** `gmail_helper.py` — zero-dep stdlib (urllib/json/ssl/base64/email), sha12 `5fd20fd379a7`, 131 lines; subcommands list/get/modify/draft/labels; **no send path by design** (archive = `modify --remove INBOX`). Host-side one-time OAuth (`gmail_consent.py`, `gmail.modify` scope, offline → durable refresh token on **gog's published client**, so no TTL cliff). Per-mailbox creds whoami-verified to distinct mailboxes.
- **Applied (F17).** Network flip + per-agent creds binds committed; gateway restarted once (pid **2118→56991**, A-7 clean, live sha `bfb3375d17f9b9b7`) to apply the restart-trigger network key + binds together. Helper bound RO→`/opt/gmail_helper.py`; each agent's own creds RO→`/creds/google.json` (pa→pfedchen, pro→soveren — single-file binds give per-agent credential isolation). `dangerouslyAllowExternalBindSources:true`; creds live in `~/.openclaw/agent-creds/` (outside the rw/git workspace).
- **Detour (→ P6-23).** The helper had deployed as an **empty directory**, not the script; fixed. Marie's `pa` container was created during that window (`recreate --all` ran *before* the host fix) → stale empty-dir mount → reported `/opt/gmail_helper.py` as a directory (correctly). Fixed by recreating `pa` **after** the host file was correct.
- **Wired (→ P6-24).** Helper documented in each agent's **TOOLS.md** (command prefix, verbs, no-send/archive-not-delete rules); **P6-22** surface-failures-loudly rule added to each **AGENTS.md**. Marie's AGENTS inbox line redirected from gog → helper.
- **Proven.** Marie: inbox + archive, live. Charlotte: Soveren triage + auto-archive, live (also discharges her TOOLS.md modify-capable deploy-check and the 6c closure's "first archive empirical confirmation"). P6-22 demonstrated working: Marie surfaced the stale-container fault loudly rather than going silent.

## End-state (anchors)
- **openclaw.json:** `network:bridge` + per-agent full `sandbox` blocks (pa, pro). Live sha `bfb3375d17f9b9b7` (== `.last-good`). Gateway pid **56991**.
- **Helper:** `~/.openclaw/agent-creds/gmail_helper.py` — 644 openclaw:staff, sha12 `5fd20fd379a7`, 5689B.
- **Creds:** `agent-creds/{pfedchen,soveren}-gmail.json` — 600 openclaw:staff, durable refresh tokens.
- **Marie (`pa`):** AGENTS `2966a8599e13`, TOOLS `d4001c96749d`.
- **Charlotte (`pro`):** AGENTS `bd0fa295eee0`, TOOLS `69876f4d110d`.
- **Containers:** `scope:agent`, `net=bridge`, `readOnlyRoot:true`, binds = workspace + `/opt/gmail_helper.py` + `/creds/google.json`. Lazy — respawn on the next agent turn.

## 6c.2 Stage 0 — VERIFY-ONLY (do NOT re-apply)
1. Gateway `healthz` 200; pid present; A-7 (live == last-good, `bfb3375d17f9b9b7` unless changed since).
2. Helper is a **file**: `[ -f agent-creds/gmail_helper.py ]` + sha12 `5fd20fd379a7` + 5689B.
3. One-shot `docker run --rm --read-only --network bridge` + binds + helper `list --max 1` returns real mail for **both** pfedchen and soveren.
4. Marie/Charlotte AGENTS+TOOLS shas as above; **P6-22** section present in both AGENTS.md.
_(If anything has drifted, characterise it before touching — Stage 0 does not re-apply.)_

## Outstanding (carried)
- **P6-1 calendar → 6c.2** (direct successor). Central decision: durable calendar OAuth (re-consent on gog's published client with calendar scope) vs the 7-day Testing calendar-backup token (**F44**). See 6c.2 opener.
- **F28 cold-reboot proof** — still pending (box has not been cold-rebooted).
- **SR-1** — operator-docs GitHub target + CoS sync mechanism; surfaces at 6d.
- **PENDING-BATCH-APPLY** (P6-9 / P6-11 / P6-12 / P6-14) — CoS runs post-roster.
- **P6-20 avatar** — Charlotte / Andreas, as the final docs-locked step.
- **Per-agent `sandbox`-block drift (P6-24)** — pa+pro now carry full sandbox blocks; re-sync them on any change to `agents.defaults.sandbox`.

---
_Attachments for 6c.2: this closure + PHASE-6-DEVIATIONS-LOG.md + MASTER-ARCHITECTURE-v6 + the 6c.2 opener. Older closures on demand only._
