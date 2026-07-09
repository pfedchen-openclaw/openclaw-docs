# OpenClaw — Project State of Nation

**Last updated:** 2026-07-09 (Session 6c.31 — v7 capstone reconciliation) — *reconciled from the P6-1…138 deltas at 6c.31 (milestone-boundary reconciliation per the doc-tier protocol). Prior full rebuild was 6a.33; this pass updates drifted facts in place and keeps the snapshot form.*
**Phase:** 6 (assistant-trio hardening) — Marie + Charlotte live; hardening toward 6d (Andreas / Chief of Staff) and the v7 capstone
**Audience:** Operator picking up the system. Assumes general technical literacy (SSH, shell, JSON, basic API concepts). Does not assume prior OpenClaw knowledge.

> **Document set.** This is one of two companion documents.
> - **PROJECT-STATE-OF-NATION.md** (this document) — what the system *is*: architecture, components, agents, dependencies. Read this to understand the system.
> - **PROJECT-RUNBOOK.md** (companion) — what to *do* when something needs fixing: credential rotation, failure recovery, command reference. Reach for that one in an incident.
>
> Cross-references between the two are explicit (e.g. "RUNBOOK §2.1"). The glossary (§5 here) is duplicated in both so each works standalone. RUNBOOK cross-references are by topic where new procedures (vault-backup, op-backup, config-repo restore) are pending its 6a.34 refresh.

## 0. Preamble

### What this is

This document describes the state of Peter Fedchenkov's OpenClaw deployment on a dedicated Mac Mini M4 as of 2026-07-09. It covers what is running on the machine, what each component does, what agents the system hosts, and which external services it depends on. Operational procedures — credential rotation, failure recovery — live in the companion PROJECT-RUNBOOK.md.

OpenClaw is an open-source AI agent framework. A single process running on the Mini hosts a team of agents — each with its own role, persona, tools, and memory — and exposes them over local channels (Telegram bots, in this deployment). All large-language-model inference happens via cloud APIs (Anthropic + OpenAI); the Mini itself runs only lightweight orchestration.

### Phase reality check (6c.31)

The architecture spec describes a 9-agent team (personal assistant, professional assistant, chief of staff, family, SDR, travel, research, finance, librarian); **as of 2026-07-09 (6c.31), 2 agents are live and have graduated well past L1 — Marie (`pa`) and Charlotte (`pro`)**. Marie holds her own send-email (`marie@fedchenkov.com`), calendar write, Drive write, and prepare-and-surface payments (2 capped virtual cards); account-creation is cleared under the "gate at value" posture. Charlotte's Soveren-triage is live but mostly dormant, awaiting a real work mailbox. Infrastructure — gateway, plugins, skills, sandbox runtime (Colima now autostarts), memory (v2 retrieval + health canary), backups, and a ~12-job launchd fleet — is in place and verified. The remaining per-agent builds run one per session: 6d Chief of Staff (Andreas, next; still pre-build), then Sophie/Leo (family; AGENTS.md/TOOLS.md derived but not yet built), then later sessions for the rest.

Where this document references future capabilities ("the nightly consolidation job…"), they are spec-canonical but not yet running. Sections flag current build status where it matters operationally.

### How to use this document

- **First-time reader:** read §1 → §3 in order for the mental model, then skim §4 (dependencies) and §5 (glossary). Then read RUNBOOK §1 for credential discipline.
- **Specific area:** §2 for infrastructure, §3 for agents, §4 for external dependencies. Each is largely self-contained.
- **Incident in progress:** you want the companion. Go to PROJECT-RUNBOOK.md.

Where this document references `F#`/`D#`/`S#`/`M#`/`SR#` numbers, those refer to rows in the now-frozen Phase-0 `DEVIATIONS-LOG.md`. Phase-6 findings use `P6-N` codes and live in the append-only `PHASE-6-DEVIATIONS-LOG.md` (**P6-1…138 as of 6c.31**) — that is the authoritative living record now. Relevant context is summarised inline; consult the logs for full row history. Live config anchor at this reconciliation: `openclaw.json` sha256-12 `3267edd8b6aa`, 10489 B (A-7 clean, pin 2026.4.22 build `00bd2cf`).

### Key facts box (standalone quick-orientation)

```
Gateway:        127.0.0.1:18789 (loopback only, launchd-supervised, gui/502)
                OpenClaw 2026.4.22 (build 00bd2cf)
Health check:   curl http://127.0.0.1:18789/healthz  → {"ok":true,"status":"live"}
Remote access:  SSH over Tailscale — Mini at 100.84.220.122 (primary)
                Screen Sharing into the openclaw GUI session (admin / GUI-bound ops)
Config root:    ~/.openclaw/  (openclaw.json, secrets.json, credentials/, scripts/, logs/, backups/)
Config sha:     openclaw.json sha256-12 3267edd8b6aa / 10489 B (A-7 clean)
Service user:   openclaw   (runtime, persistent GUI login)   |   Admin user: pfedchenkov
1Password:      https://my.1password.eu  →  OpenClaw vault (source of truth)
Config backup:  github.com/pfedchen-openclaw/openclaw-config (private, sanitised) — F72
launchd fleet:  ~12 live ai.openclaw.* jobs (gateway, colima + socket-watchdog, backups,
                heartbeat watchdogs, session/memory/cost/reply/cred/chat-bloat canaries) — §2.8
Agents:         9 configured; Marie/pa + Charlotte/pro LIVE (graduated past L1); next: 6d Andreas (CoS); Sophie/Leo derived-not-built
Full command reference + recovery procedures: PROJECT-RUNBOOK.md
```

## 1. System at a glance

### What OpenClaw is

OpenClaw is an AI agent framework. A single Node.js process — the "gateway" — hosts a team of agents, each with its own configuration, persona, tools, and memory. The gateway runs on the Mac Mini. All LLM inference happens via cloud APIs (Anthropic Claude as primary, OpenAI as fallback); the Mini itself runs lightweight orchestration only.

In this deployment, agents are reached by humans via Telegram bots — one bot per agent persona. An agent's response to a message is the output of: receive message → pull relevant context from memory → call LLM → return response. Heartbeats (scheduled awareness checks) and inter-agent messaging operate through the same gateway.

### The two-user architecture

The Mac Mini hosts two macOS user accounts with strict separation of concerns:

- **`pfedchenkov`** is the human admin account. Homebrew lives here (`/opt/homebrew`, installed and owned under the admin user; the `openclaw` user reads `/opt/homebrew/bin` via its `.zshenv` PATH per the F9 fix). 1Password authentication and any admin GUI work happen here.
- **`openclaw`** is the service account. The agent runtime — gateway process, per-agent state, credentials, logs, backups — lives entirely under this account.

This separation is a deliberate security boundary (D7): a compromised agent runtime cannot escalate to admin-level system changes without first escaping the `openclaw` user account. Agents run as `openclaw`; a rogue skill or prompt injection cannot `sudo` without the admin password.

**GUI session reality (important; cold-boot caveat P6-43).** FileVault is on and auto-login is disabled (D11 — Tahoe enforces mutual exclusivity between the two, and disk encryption was chosen over reboot convenience). Consequently, **a COLD boot halts at the FileVault pre-boot login screen and needs a physical or Screen-Sharing unlock** — `launchd` `KeepAlive` cannot help, because nothing (Tailscale, SSH, the gateway) comes up until the disk is unlocked and the `openclaw` account is logged in (P6-43). *Once logged in*, `openclaw` holds a **persistent GUI login session** (`gui/502`) that hosts the ~12-job launchd fleet (§2.8) and the Login Items (Obsidian, Tailscale). So the service account *does* run a GUI session in normal operation — it is simply established manually after each cold boot rather than via auto-login. (Colima now autostarts within that session — §2.4.)

SSH from Peter's MacBook reaches the Mini over Tailscale (`100.84.220.122`). The bulk of operational work — anything touching `~/.openclaw/` — runs as `openclaw`. Admin work (brew installs, Login Items, GUI tools) and GUI-bound launchd lifecycle operations (`bootstrap`/`bootout`, per D20) run via Screen Sharing into the openclaw GUI session or as `pfedchenkov`.

### Physical and network shape

```
   Peter MacBook  <-- SSH over Tailscale / Screen Share -->  Mac Mini M4 (headless)
                          (100.84.220.122)                    |
                                                              +-- openclaw user (GUI session, gui/502)
                                                              |    |-- Gateway process :18789 (launchd KeepAlive)
                                                              |    |-- Colima Docker VM (autostarts + socket-watchdog)
                                                              |    |-- ~12 launchd jobs (backups, watchdogs, canaries) §2.8
                                                              |    |-- Per-agent runtime + memory v2 (sqlite-vec, local)
                                                              |    |-- Credentials + encrypted backups
                                                              |    +-- Login Items: Obsidian, Tailscale
                                                              |
                                                              +-- pfedchenkov user
                                                                   +-- Homebrew, GUI/admin tools

   Peter Telegram  <-- Telegram Bot API -->  Gateway
                                              |
                                              v
                            Anthropic / OpenAI / Brave / Google / Google Drive APIs
```

The gateway listens only on loopback (`127.0.0.1:18789`) — it is NOT exposed to the network. External reach happens via the Telegram bot API (the gateway holds the bot tokens and receives user messages indirectly through Telegram's servers) and direct outbound calls to cloud providers. Tailscale provides the secure remote-access path for SSH/Screen-Sharing; it does not change the gateway's loopback binding.

### The three-layer mental model

1. **Gateway process.** A Node.js process supervised by macOS `launchd`. Single point of supervision: if it's running and healthy, the system is up. If it isn't, nothing else matters until you fix it. RUNBOOK §2.1 covers recovery.
2. **Per-agent runtime.** Conceptual abstraction — physically a code path inside the gateway, parameterised by agent id. Each agent has its own config block in `openclaw.json`, workspace dir at `~/.openclaw/workspace-{id}`, memory store at `~/.openclaw/memory/{id}.sqlite`, and session store at `~/.openclaw/agents/{id}/sessions/sessions.json`.
3. **External channels and APIs.** Telegram bots (4 currently configured) handle inbound user messages. Cloud APIs handle outbound work. The gateway routes between them.

## 2. What's running on the Mini

### 2.1 The gateway process

A single Node.js process is the heart of the system.

- **Supervisor:** macOS `launchd` via `~/Library/LaunchAgents/ai.openclaw.gateway.plist` (sha12 `5e4487cdf559`, label `ai.openclaw.gateway`, in the `gui/502` domain).
- **Listens on:** `127.0.0.1:18789` (loopback only)
- **Version:** OpenClaw 2026.4.22 (build `00bd2cf`) — pinned (D1; do not upgrade without an explicit decision — 2026.5.12 exists, held per M4 during agent builds).
- **Health check:** `curl http://127.0.0.1:18789/healthz` returns `{"ok":true,"status":"live"}` (HTTP 200) when alive.
- **Supervision behaviour:** the plist has `KeepAlive` + `RunAtLoad`, so the gateway **auto-restarts on crash** and **auto-starts once the openclaw GUI session is up**. PID is ephemeral (changes across restarts) — never anchor on a specific PID. Crash recovery needs no manual action. **Reboot recovery is only automatic after a warm login: a COLD boot stops at the FileVault screen and needs a physical/Screen-Sharing unlock first (P6-43) — `KeepAlive` cannot bridge that gap.** Once the disk is unlocked and `openclaw` is logged in, the gateway (and the rest of the fleet) come up on their own; empirically confirmed across operator-initiated reboots (S5, 6a.27) with this cold-boot qualifier.

To inspect the gateway, run as `openclaw`:

```bash
launchctl print gui/$(id -u)/ai.openclaw.gateway | head -3   # registration (head-3 not head-1, F69)
curl http://127.0.0.1:18789/healthz                          # liveness
openclaw memory status                                       # provider/embedding readiness
```

Restart procedure (rare; only when health check fails and auto-restart has not kicked in) is the **F45 restoration ceremony** in RUNBOOK §2.1. It requires a GUI session on the openclaw account (per D20 — `bootstrap`/`bootout` are GUI-Terminal-only; `launchctl print` is SSH-safe), so Screen Sharing from the admin MacBook in the current setup.

### 2.2 Plugin entries

Plugins are subsystems the gateway loads at startup, configured under `plugins.entries` in `openclaw.json`. Current set (4 entries):

| Plugin | Purpose |
|--------|---------|
| `anthropic` | Anthropic API client for Claude inference. Default LLM provider. |
| `openai` | OpenAI API client. Inference fallback (`gpt-4o`) and (per D22) memory embeddings (`text-embedding-3-small`). |
| `brave` | Brave Search API client. Web search for agents that need it. |
| `browser` | Browser automation subsystem. SSRF policy restricts to non-private networks (`dangerouslyAllowPrivateNetwork: false`). |

API keys resolve via SecretRef references in `openclaw.json` → `secrets.json` (Brave, OpenAI inference) and per-agent `auth-profiles.json` (Anthropic, and the OpenAI embedding key — see §2.5 / F61). See RUNBOOK §1 for the full resolution chain.

### 2.3 Skills

Skills are bundled or installed packages that give agents specific capabilities (e.g. "send an email", "search the web", "read a PDF"). Two sources:

- **Bundled skills.** Ship with OpenClaw. Active set is constrained by the top-level **`skills.allowBundled`** allowlist in `openclaw.json` (F11 closed; default is "everything unless disabled", so the allowlist is the security control). Current 12-entry allowlist: `gog`, `summarize`, `obsidian`, `github`, `weather`, `healthcheck`, `model-usage`, `session-logs`, `openai-whisper`, `openai-image-gen`, `goplaces`, `blogwatcher`. A skill in the allowlist still shows `△ Needs setup` until its external binary/credentials land (e.g. `gog` needs `brew install gogcli`; `goplaces` needs a Places API key).
- **ClawHub skills.** Optional installs from the community registry. None currently installed; all installs go through the **D2 source-review gate**. Per F57, `openclaw skills search --json` returns a thin manifest (no repo URL/deps/license), so pre-install review requires web-side investigation (ClawHub UI + author GitHub); very-new GitHub accounts are auto-rejected. `skills-manifest.md` (per §4.1 #6) is authored at first install — an agent-build operation (6b/6c).

To list: `openclaw skills list`. To search: `openclaw skills search <name> --json`. **Never install a ClawHub skill without a web-side source review.**

### 2.4 Docker sandbox runtime

Some skills execute code in a sandboxed container. The Mini uses **Colima** (0.10.1) as the Docker runtime, chosen over Docker Desktop (D17). Colima runs a Linux VM under the `openclaw` account.

- **Sandbox image:** `openclaw-sandbox:bookworm-slim` (Debian Bookworm base). Security profile: `mode:all` / `scope:agent` / `readOnlyRoot:true`. The **sandbox-egress work (the old 6c.1 blocker) is resolved** — agents now have the outbound path they need to act externally (browser, `gmail_helper`, `gog`, reply-watcher), where the earlier build ran fully network-isolated and could think but not act.
- **Status check:** `colima status` from the `openclaw` shell.
- **Autostart (F28 resolved).** Colima **now autostarts** via the `ai.openclaw.colima` LaunchAgent (`RunAtLoad`), backed by `ai.openclaw.colima-socket-watchdog` (a periodic check that restarts Colima / re-heals the Docker socket if it drops). The old "manual `colima start` after every reboot" caveat is gone. (`brew services start colima` is still not the mechanism — the LaunchAgent owns lifecycle.)
- **Docker Desktop retirement** (F29/F30) is partially complete — Login Items, privileged helper, and full uninstall remain deferred cleanup.

If Colima is down, sandbox-requiring skills fail; everything else (inference, memory, Telegram routing) continues — sandbox failure is degraded-mode, not system-down. The socket-watchdog exists to shrink that window.

### 2.5 Memory subsystem

Per agent, a local SQLite database at `~/.openclaw/memory/{agentId}.sqlite` stores conversation history, daily notes, and (eventually) a knowledge graph. Search uses both full-text (FTS5) and vector indexes (via the `sqlite-vec` extension at `~/.npm-global/lib/node_modules/openclaw/node_modules/sqlite-vec-darwin-arm64/vec0.dylib`).

Text-to-vector conversion happens via an **embedding API**. Per **D22** (closed, written at 6a.28), this deployment uses **OpenAI `text-embedding-3-small`**, `fallback: none` (explicit hard-fail over silent degradation). The embedding key resolves from each agent's `auth-profiles.json`, **not** `secrets.json` (F61 — provisioned into all 10 agent dirs); any new agent created via `openclaw agents add` needs `openai:default` provisioned before its first embedding call.

**Memory-v2 is live.** The retrieval/index stack is now exercised, not dormant: a `memory-health` canary (launchd, §2.8) periodically re-checks index integrity, and the memory gate was **recalibrated PASS at 6c.30 (P6-137)**. The stores are **no longer empty** — the two live agents carry real indexed content: **Marie (`pa`) 39/39 files indexed (314 chunks)**, **Charlotte (`pro`) 35/35**. Both report `provider: openai`, model `text-embedding-3-small`, **Vector: ready / FTS: ready**, no error. The 7 unbuilt agent skeletons still have empty stores (0 files/chunks) — expected pre-build, not a fault.

Two distinct CLI surfaces touch this area:

- **`openclaw memory *`** — memory files (long-term): `status`, `search`, `index`, `promote`, `rem-harness`, `rem-backfill`.
- **`openclaw sessions *`** — conversation session stores (transcripts): `sessions --agent <id>`, `--all-agents`, `cleanup`.

Clearing one does not affect the other. Recovery procedures: RUNBOOK §2.5.

### 2.6 Config files (the canonical paths)

All operational state lives under `~/.openclaw/`. The files that matter most:

| File | Approx. size | Purpose |
|------|--------------|---------|
| `openclaw.json` | ~10.2 KB (10489 B; sha256-12 `3267edd8b6aa`) | Main config. Agents, plugins, channels, bindings, secrets resolution, browser policy, media caps, heartbeat cadence, `agents.defaults.memorySearch`/`contextTokens`. Mode 0600 openclaw:staff. **Holds the 4 Telegram bot tokens inline** (not SecretRefs) — see §2.8 for how the config repo handles this. |
| `secrets.json` | ~245 B | API keys in plaintext (mode 0600). SecretRef resolution target. Holds `OPENAI_API_KEY` + `BRAVE_API_KEY`. |
| `agents/main/agent/auth-profiles.json` | ~253 B | The inheritance-template auth store (Anthropic key + the provisioned `openai:default`). All 10 agent dirs (main + 9) carry their own copy (materialise-once, F25). |
| `agents/main/agent/models.json` | ~2 KB | Per-agent model config. **Runtime-mutable**: `openclaw secrets *` and `openclaw infer *` rewrite it. Re-baseline its sha12 at every session opener. |
| `credentials/*` | varies | `gog-client-secret.json`, `gog-keyring-password` (45 B), `calendar-backup-token.json`, `op-backup-passphrase` (45 B), `telegram-pa-allowFrom.json`, `telegram-pairing.json`. All mode 0600. |

Canonical sha12 + byte anchors are captured in the live `PHASE-6-DEVIATIONS-LOG.md` (openers/closures re-baseline them each session); drift at session boundaries is a signal to investigate before assuming benign (RUNBOOK §2.7). The most-engineered artefact (`openclaw.json`) now has an off-Mini backup — see §2.8.

### 2.7 Logs

Two log files under `~/.openclaw/logs/`:

- **`gateway.log`** — lifecycle events: startup, shutdown, plugin loads, channel pairings, reload events, normal activity.
- **`gateway.err.log`** — security/audit: auth failures, rate-limit hits, restart classifiers, anomalies. Restart-trigger classifier lines land here, not in `gateway.log`.

Inspect with `tail -50 ~/.openclaw/logs/gateway.log` or `grep -i <pattern>`. A supplementary bundled JSON-line logger writes to `/tmp/openclaw/openclaw-YYYY-MM-DD.log` (ephemeral by design, cleared on reboot — F12); the launchd-managed `gateway.log` / `gateway.err.log` are the reboot-persistent forensics channel.

### 2.8 Scheduled jobs, backups, and the config repo

**Scheduling is launchd, not cron** (F55 — Tahoe TCC blocks `crontab` writes from SSH; launchd is the supported path). The fleet has grown from 2 jobs to **~12 loaded `gui/502` LaunchAgents** (all label `ai.openclaw.*`, all last-exit 0):

| Job | Schedule | Purpose |
|-----|----------|---------|
| `ai.openclaw.gateway` | KeepAlive + RunAtLoad | The gateway process itself (§2.1). |
| `ai.openclaw.colima` | RunAtLoad | Autostarts the Colima Docker VM at login (F28 resolved, §2.4). |
| `ai.openclaw.colima-socket-watchdog` | StartInterval | Periodic check that re-heals Colima / the Docker socket if it drops. |
| `ai.openclaw.vault-backup` | 04:00 daily | Obsidian vault → Google Drive sync + dated archive. |
| `ai.openclaw.heartbeat-watchdog` | StartInterval | Watches agent heartbeat liveness; nudges/recovers a stalled beat. |
| `ai.openclaw.heartbeat-nightwindow` | StartCalendarInterval | Flips heartbeat cadence into/out of the overnight window (active 7am–11pm). |
| `ai.openclaw.session-freshstart` | StartCalendarInterval | Daily session reset — trims accumulated session state to control cost/bloat. |
| `ai.openclaw.memory-health` | StartCalendarInterval | Memory-v2 index-integrity canary (§2.5). |
| `ai.openclaw.cost-notify` | StartInterval | Daily-spend canary — surfaces cost (Anthropic Console authoritative; recent days ~$5–16). |
| `ai.openclaw.reply-watcher` | StartInterval | Bounded Gmail poller on live creds (dryrun/notify/poke modes, daily-capped, dedup). |
| `ai.openclaw.cred-save-watch` | StartInterval | Drains queued credential-save requests (host-mediated `op` writes). |
| `ai.openclaw.chat-bloat-reset` | StartInterval | Intra-day `:main`-bloat auto-reset net. |

**Descoped jobs (D24).** `calendar-backup` and `security-audit` remain descoped and durable across login cycles via the `*.plist.disabled-D24` rename (a non-`*.plist` suffix breaks launchd's auto-load glob — F67; proven through a forced reboot). They sit at `~/Library/LaunchAgents/ai.openclaw.{calendar-backup,security-audit}.plist.disabled-D24` (781 B / 835 B), absent from the active table (`launchctl print` → `Could not find service`). Note Marie's calendar *write* landed via the **direct Google API (F46)**, not the gog-based `calendar-backup` job — so that job stays descoped; `security-audit` re-enables at the first agent consumer build. Re-enable = rename back to `.plist`, `bootstrap gui/502`, and apply the F68 PATH lesson via `EnvironmentVariables` for any `node`/`op`/PATH-dependent job.

**Backups (Stages 4–5):**

- **Obsidian vault → Google Drive (daily).** `scripts/vault-backup.sh` runs at 04:00 via the launchd job: `rclone sync ~/obsidian-vault` → `gdrive-personal:Vault/`, with a dated archive copy to `gdrive-personal:Vault-Archive/YYYY-MM-DD/`. Confirmed clean fire (rclone exit 0). Satisfies the §4.3/§4.11 daily off-Mini vault-backup requirement.
- **1Password vault → encrypted local snapshot.** `scripts/op-backup.sh` produces an AES-256-CBC-encrypted snapshot at `~/.openclaw/backups/onepassword-vault-YYYY-MM-DD-HHMMSS.enc` (latest ~60 KB, symlinked `onepassword-vault-latest.enc`), passphrase at `credentials/op-backup-passphrase` (3-way held). **Interactive / manual-trigger only** for now — daily automation is deferred (F70: 1Password Service Accounts need a paid Teams/Business tier; revisit when unattended backup is a real need).
- **rclone** v1.74.2, scope-confined `gdrive-personal` remote (`rclone.conf` mode 0600, `drive.scope` restricted, pinned `root_folder_id`). The encrypted `.enc` snapshots are *not* pushed offsite by default (rides with F70).

**Config repo (F72, new at 6a.33).** `~/.openclaw` is now a git repo pushed to the **private** GitHub repo `pfedchen-openclaw/openclaw-config` (root commit `2067ad2`). A default-deny `.gitignore` tracks only safe paths: `openclaw.sanitised.json` (the live config with the 4 bot tokens redacted to placeholders — produced by `scripts/sanitise_openclaw.py`), the four `scripts/`, copies of the four plists under `launchagents/`, `.gitignore`, and `README.md`. **No secrets are tracked** — live `openclaw.json`, `secrets.json`, `credentials/`, `agents/`, `backups/*.enc` are all gitignored. **The sanitised config is a snapshot**: after any change to `openclaw.json` (6b PA build, token rotation, channel edit), re-run `sanitise_openclaw.py --write` + commit + push to refresh it. Eventual end-state (deferred): refactor the inline bot tokens to SecretRefs (spec step 16) so `openclaw.json` tracks verbatim and the sanitiser retires.

### 2.9 Obsidian (knowledge vault)

Obsidian **1.12.7** is installed at `/Applications/Obsidian.app`, runs as `openclaw` (a registered **Login Item**, so it auto-starts at the openclaw GUI login alongside Tailscale), and has the vault at `~/obsidian-vault` open (`.obsidian/` config present). Vault top-level structure: `_Private`, `_TEMP`, `Archive`, `Learning` (Biographies / Copywriting / Investment / Storytelling), `Library`, `Meetings`, `Projects`, `Templates`, plus `README.md` and an `.obsidian-ignore` marker. The vault is backed up daily to Google Drive (§2.8). Obsidian Sync (first-party) — Peter holds a subscription (Scenario A); the per-vault Sync toggle is confirmed ON and syncing (verified by Peter at the Mini GUI, 6a.34), with the gdrive vault-backup providing a second, independent offsite copy.

## 3. The agent roster

### 3.1 The team

| Agent id | Persona | Role | Telegram | Build status |
|----------|---------|------|----------|--------------|
| `pa` | Marie | Personal Assistant — calendar, personal email, errands, payments | Dedicated bot (paired) | **LIVE, graduated past L1** (6b → 6c.x); own send-email `marie@fedchenkov.com`, calendar + Drive write, prepare-and-surface payments (2 capped virtual cards), account-creation cleared ("gate at value"). Model **anthropic/claude-opus-4-7** (per-agent override for real chats). |
| `pro` | Charlotte | Professional Assistant — Soveren-triage; work email/exec ops await a real work mailbox | Dedicated bot (paired) | **LIVE** (6c); Soveren-triage running but **mostly dormant** pending a live work mailbox. Model **sonnet-4-6** (defaults primary; P6-15 — Opus is the v2 posture). |
| `cos` | Andreas | Chief of Staff — strategic synthesis, weekly briefings, agent coordination | Dedicated bot | Build scheduled **6d** (next); still **pre-build**. Model opus-4-7. |
| `leo` | Sophie | Family agent — care for Leo, warm/family voice | Dedicated bot | AGENTS.md/TOOLS.md **derived but not yet built** (after 6d). |
| `sdr` | (TBD) | Sales Development — outbound prospecting | Indirect (via CoS) | Later session |
| `travel` | (TBD) | Travel — trips, bookings, logistics | Indirect | Later session |
| `research` | (TBD) | Research — deep investigation, market intel | Indirect | Later session |
| `finance` | (TBD) | Finance — meticulous, expense + wealth tracking | None (write-only) | Later session |
| `librarian` | (TBD) | Librarian — organisation, archival, audit | Indirect | Later session |

**Persona naming.** Each agent presents to Peter as a named character with a defined voice (Marie warm/anticipatory, Charlotte Goldman-precise, Andreas structured/strategic, Sophie warm/family per architecture §3.6). First-name only across the team, no surnames-of-record (D12) — clean emails (Marie's `marie@fedchenkov.com` is live; the rest land as their builds ship) and an "AI assistant" posture rather than implied human staff. The 5 remaining personas are named at their build sessions.

**Special case — `leo` agent.** The agent id is `leo` (after the family member it serves); the persona Peter interacts with is "Sophie" (@sophie_pf_bot). Intentional (D13) — treat `leo` as the agent id everywhere in config, "Sophie" as the voice.

### 3.2 Build status

As of 2026-07-09 (6c.31), **Marie and Charlotte have authored, deployed, indexed workspaces and have run end-to-end through the real gateway** (F26 done). The build sequence:

- **Session 6b (DONE)** — Personal Assistant. Marie live; since graduated well past L1 — own send-email `marie@fedchenkov.com`, calendar + Drive write, prepare-and-surface payments (2 capped virtual cards), account-creation cleared. Memory index 39/39 (314 chunks).
- **Session 6c (DONE)** — Professional Assistant. Charlotte live; Soveren-triage running but mostly dormant awaiting a real work mailbox; model sonnet-4-6 (P6-15). Memory index 35/35.
- **6c.x hardening (DONE — the run of sessions since)** — sandbox egress resolved (agents act externally: browser, `gmail_helper`, `gog`, reply-watcher); Colima autostart; the ~12-job launchd fleet; memory-v2 + health canary (gate PASS at 6c.30, P6-137); cost-hardening ([P6-85]: Marie heartbeat 30m→2h, `contextTokens` 200K→120K) with a `cost-notify` canary; payments-v1 prepare-and-surface; host-mediated `op` access. The old "can think but can't act externally" blocker is gone.
- **Session 6d** — Chief of Staff (Andreas), next. Takes on maintenance-cadence ownership (D19) + operator-docs stewardship (D23/SR-1); builds the morning-briefing + nightly-consolidation jobs.
- **After 6d** — Sophie/Leo (family; AGENTS.md/TOOLS.md derived, not yet built), then SDR, Travel, Research, Finance, Librarian (order by operational priority).

The 4 bots (Marie/Charlotte/Andreas/Sophie) are channel-added, `dmPolicy: pairing`, agent-bound, providers running/connected (F26 architectural closure). **Marie + Charlotte are pairing-approved (Peter whitelisted) and in daily use**; Andreas/Sophie pair at their build sessions.

### 3.3 How agents are invoked

Three invocation paths:

1. **Direct Telegram message.** Peter messages one of the 4 dedicated bots; the Telegram bot API delivers to the gateway, which routes to the agent runtime, which calls the LLM and replies. (Per-bot peer authorisation is the `dmPolicy: pairing` + `openclaw pairing approve` flow, distinct from the OpenClaw `devices` table — F26.)
2. **Heartbeat.** Scheduled periodic awareness checks (§3.4).
3. **Inter-agent message.** One agent sends a request to another (e.g. Sophie asks PA to book Leo's appointment). The receiving agent treats it as a request subject to its own autonomy rules — never bypasses human approval gates (§3.5).

### 3.4 Heartbeat cadence

Heartbeats are periodic checks an agent runs autonomously. Two modes:

- **Lightweight heartbeat.** Minimal context (~2–3K tokens; AGENTS.md + today's memory). Checks new inputs (Gmail, calendar, channel scan), flags urgent items, doesn't draft. ~$0.005–0.05 each.
- **Full heartbeat.** Full context (~8–12K tokens: SOUL.md + AGENTS.md + USER.md + today's memory + relevant items). Drafts responses, triages inbox, makes scheduling decisions. ~$0.03–0.20 each.

Cadence: active hours (7am–11pm), with overnight (11pm–7am) suppressed via `ai.openclaw.heartbeat-nightwindow`; deep-work hours queue non-urgent items so Peter isn't pinged during focus blocks. Per-agent cadence is authored into each AGENTS.md at build time. **Cost-hardening [P6-85]:** Marie's live beat was lowered from every 30m to **every 2h**, and `agents.defaults.contextTokens` from 200K to **120K**; a `heartbeat-watchdog` recovers a stalled beat, and the `cost-notify` canary tracks daily spend (Anthropic Console authoritative; recent days ~$5–16). Finance runs a weekly/monthly cadence, no forced daily heartbeat.

### 3.5 Inter-agent coordination

Three tiers, ordered by trust and scope (architecture §3.8):

- **Tier 1 — Shared daily digests.** Each agent writes a structured daily summary to `~/.openclaw/shared/daily-digests/YYYY-MM-DD/{agent}.md`. The shared folder structure exists (`master.md` anchor `cb2dd25b5828`) and **Marie's daily digest now emits** (the external-action path is live). The **CoS-side consolidation / morning-briefing** that reads all agents' digests is still **the 6d Andreas build (launchd, not `openclaw cron`)**, not yet running. In v1 only PA + ProA emit digests.
- **Tier 2 — Directed messaging.** Sophie → PA; CoS → SDR, ProA; Travel → PA, ProA; PA ↔ ProA (calendar conflicts). **Finance receives nothing inbound** — write-only.
- **Tier 3 — Ad-hoc queries.** For info not in digests, agents ask targeted questions; each AGENTS.md specifies allowed partners and topics.

**Loop protection.** If two agents exchange >10 messages in one hour, the exchange pauses and CoS alerts Peter. Richer inter-agent rate limiting is **S11** at the 6d CoS build.

### 3.6 Per-agent state on disk

Each agent's state lives in four locations:

- **Workspace.** `~/.openclaw/workspace-{id}/` — the authored templates (SOUL.md, AGENTS.md, etc.). **Marie (`pa`) and Charlotte (`pro`) are authored + deployed**; the other 7 dirs exist provisioned-inert (F58), templates unauthored pre-build. (Sophie's AGENTS.md/TOOLS.md are derived from the current Marie/Charlotte canon but not yet deployed.)
- **Memory store.** `~/.openclaw/memory/{id}.sqlite` — sqlite-vec-backed local DB, 0600. **`pa` and `pro` carry real indexed content** (39/39 and 35/35 files; §2.5); the 7 unbuilt agents' stores are still empty.
- **Daily notes + REM data.** `~/.openclaw/workspace-{id}/memory/` — daily notes, `MEMORY.md`, `.dreams/`. Populated for the two live agents; absent for the unbuilt ones.
- **Session store.** `~/.openclaw/agents/{id}/sessions/sessions.json`. Live entries for `pa`/`pro`; a daily `session-freshstart` job trims accumulated state (§2.8).

The split between "memory" (long-term, intentional) and "sessions" (transcript history) is operationally meaningful: clearing one does not affect the other. Recovery: RUNBOOK §2.5.

## 4. External dependencies

| Service | Criticality | Credential location | Failure mode | Renewal cadence |
|---------|-------------|---------------------|--------------|-----------------|
| Anthropic API | System-down (default inference) | per-agent `auth-profiles.json` | Inference 401 → no agent responses | Annual or on compromise |
| OpenAI API | Degraded (fallback inference + embeddings, D22) | `secrets.json` (inference) + `auth-profiles.json` (embeddings, F61) + 1Password | Fallback unavailable; memory writes fail | Annual or on compromise |
| Brave Search API | Degraded (web search) | `secrets.json` + 1Password | Brave-using skills fail | Annual or on compromise |
| Telegram Bot API | System-down (only inbound channel) | inline in `openclaw.json` + per-bot 1Password Notes | No inbound messages reach agents | Only on compromise |
| Google Workspace (Gmail + Calendar + Drive) | Degraded (Marie's `marie@fedchenkov.com` send-email, calendar + Drive write) | `credentials/gog-*` + file keyring; calendar/Drive via direct Google API (F46) | Mail/calendar/Drive integrations fail | OAuth token refresh; Production flip still pending (D24) |
| Google Drive (rclone) | Degraded (vault backup target) | `rclone.conf` (scope-confined gdrive-personal) | Daily vault backup fails | Token refresh; per rclone remote |
| 1Password (my.1password.eu, EU) | Recovery-critical (source of truth, 35 items) | User-account credentials; `op` CLI | Cannot rotate/restore credentials | Annual `op` re-auth; account password at Peter's discretion |
| Obsidian Sync | Optional (vault sync; gdrive backup is independent) | Peter's Obsidian subscription | Vault sync stops; gdrive backup unaffected | Subscription renewal |
| GitHub (`pfedchen-openclaw`) | Recovery-relevant (config repo F72 + future docs/workspace repos) | openclaw Ed25519 key (`SHA256:RxhIfm3w…DPSA`) | Cannot push/pull config backup | n/a |
| Domain registrar (fedchenkov.com) | Recovery-critical for Phase G (Workspace, custom email) | Peter's registrar account | Workspace setup blocked | Annual registration renewal |
| ClawHub registry | Optional (skill installs) | n/a (read-only public) | Cannot install new ClawHub skills | n/a |
| npm registry | Recovery-relevant (openclaw package) | n/a (read-only public) | Cannot update openclaw package | n/a |

**Tailscale.** In active use — the Mini sits on the tailnet at `100.84.220.122` and SSH/Screen-Sharing reach it over Tailscale (primary remote path). Tailscale runs as a Login Item under the openclaw GUI session, so it comes up at login (not pre-login — see D11). The gateway's loopback binding is unaffected.

**No dedicated Apple ID (D25).** The Mini runs with no Apple ID — stronger iCloud/Find-My isolation than a dedicated ID, no App-Store dependency surfaced to date. 1Password holds dormant Apple-ID credentials as a fallback. Revisit only if an App-Store-only install becomes required.

**Hosting and physical location.** The Mac Mini is on Peter's home network. No cloud hosting for the agent runtime — all orchestration is local. Internet access required for cloud API calls; the tailnet provides remote operator access.

## 5. Glossary

*(Duplicated verbatim in RUNBOOK §4 so each document is self-sufficient.)*

- **SecretRef.** A reference in `openclaw.json` pointing at a secret rather than embedding it. Resolves via `secrets.json`, auth-profiles, or filesystem credential, depending on the surface.
- **Auth-profiles.** A resolution surface for some credentials (Anthropic; the OpenAI embedding key per F61). Materialise-once: rotations require explicit re-materialise per agent dir (F25); gateway restart alone won't refresh.
- **F-row, D-row, S-row, M-row, SR.** Tracking categories in the now-frozen Phase-0 `DEVIATIONS-LOG.md`. F = flag/finding (a way the live system differs from spec/expectation). D = decision (committed choice + rationale). S = strategic risk / sub-fact. M = maintenance item (recurring/scheduled hygiene). SR = standing reminder carried across openers.
- **P6-N.** The Phase-6 finding codes (P6-1…138 as of 6c.31) in the append-only `PHASE-6-DEVIATIONS-LOG.md` — the authoritative living record for the current phase.
- **`allowFrom` file.** Per-channel whitelist of allowed inbound sender IDs. Only PA has one (`telegram-pa-allowFrom.json`).
- **Heartbeat.** Scheduled per-agent awareness check. Lightweight (~2–3K, frequent) or full (~8–12K, less frequent). See §3.4.
- **Autonomy level (L0–L5).** Per-agent operational latitude. L0 = read-only, L5 = proactive. Per-agent progression per architecture §3.3.
- **gog.** The Google API wrapper (Gmail + Calendar; gogcli 0.14.0). Stores OAuth tokens in a JWE **file** keyring under `~/.openclaw/credentials/` (D16, chosen over macOS Keychain for headless/cron/SSH friendliness), unlocked by `GOG_KEYRING_PASSWORD`.
- **Colima.** The Docker runtime running the sandbox VM under openclaw (D17, replacing Docker Desktop). **Now autostarts** via `ai.openclaw.colima` + a socket-watchdog LaunchAgent (F28 resolved); no manual `colima start` needed.
- **vault-backup / op-backup.** The two backup pipelines (§2.8): Obsidian vault → Google Drive daily (launchd 04:00); 1Password vault → encrypted local `.enc` snapshot (interactive, F70).
- **Config repo.** The private GitHub backup of the sanitised config + scripts + plists (`pfedchen-openclaw/openclaw-config`, F72). Holds no secret values.
- **`.disabled-D24`.** Plist suffix used to durably descope a launchd job — a non-`*.plist` name that login auto-load skips (F67). Re-enable = rename back + `bootstrap`.
- **ClawHub.** The community skill registry. Read-only via `openclaw skills search --json`. All installs go through the D2 source-review gate (F57 — the CLI manifest is too thin to substitute for review).
- **F14 chat-layer linkification.** The chat interface auto-converts dotted-TLD strings (`.json`, `.md`, `.sh`) into links, obscuring the bytes. Defence: sha12 + byte-length proxies; shell variables for paths. (Related zsh hazard: `#` is not a comment in a non-`interactive_comments` shell — never paste inline `#` comments into Mini command blocks.)
- **F17 staging pattern.** Stage credential/config edits as `<filename>.new` in the same directory, then rename-only `mv` + `chmod 600` + `chgrp staff`. Never `mv` from `/tmp`.
- **F25 re-materialise.** Auth-profile-resolved credentials require explicit per-agent re-materialise after rotation; restart alone won't refresh.
- **F44.** The calendar-backup OAuth 7-day TTL (External/Testing). Currently moot — calendar-backup is descoped (D24); resolves at the Phase G Production flip when calendar work resumes.
- **F45 ceremony.** The gateway restoration procedure for when `KeepAlive` auto-recovery hasn't kicked in. GUI-session-bound (D20). See RUNBOOK §2.1.
- **Daily digest.** Per-agent structured summary at nightly consolidation, `~/.openclaw/shared/daily-digests/YYYY-MM-DD/{agent}.md`. CoS reads all.
- **Re-baseline.** Capturing fresh sha12 + byte anchors for a runtime-mutable file (notably `models.json`) at session boundaries to detect inter-session drift.
- **Tailscale.** The mesh-VPN providing the Mini's remote-access path (`100.84.220.122`). Login Item under the openclaw GUI session; not up pre-login (D11).

---

*Operational procedures — credential rotation, failure recovery, command reference — are in the companion PROJECT-RUNBOOK.md.*

**End of document.**
