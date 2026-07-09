# OpenClaw — Project Runbook

**Last updated:** 2026-07-09 (Session 6c.31 — v7 capstone reconciliation)
**Phase:** 6c (hardening Marie + Charlotte toward the v7 capstone; before Andreas/6d)
**Audience:** Operator with a specific problem to solve right now. Assumes you've read PROJECT-STATE-OF-NATION.md at least once, or are willing to cross-reference it.

> **Document set.** This is one of two companion documents.
> - **PROJECT-STATE-OF-NATION.md** (companion) — what the system *is*: architecture, components, agents, dependencies. Read that to understand the system.
> - **PROJECT-RUNBOOK.md** (this document) — what to *do*: credential rotation, failure recovery, command reference. This is the incident-response document.
>
> Cross-references to the companion are explicit (e.g. "STATE-OF-NATION §3"). The glossary (§4 here) is duplicated from the companion so this document works standalone in an incident.

## 0. Preamble

### When to use this document

Reach for this document when something needs doing or fixing: a credential needs rotating, the gateway is down, an API is returning 401, a memory index looks stale. If instead you're trying to understand *what the system is* — the architecture, the agent roster, what depends on what — that's the companion (PROJECT-STATE-OF-NATION.md).

### The cardinal rule

When in doubt: **read-only investigation only; surface findings; wait for direction.** Every state-changing operation in this document has a "pause first" character. The system is mid-build (Phase 6c) and most of its value is latent — the cost of a wrong write is high, the cost of waiting is low. §2.8 is the explicit autonomous-vs-escalate boundary; when unsure, treat the situation as escalate.

`F#` / `D#` / `S#` / `M#` and `P6-N` references point at rows in the deviations logs. The **live** log is `PHASE-6-DEVIATIONS-LOG.md` — P6-N codes, and any new F#/D#/S# findings raised during Phase 6 now sit alongside them there (additive-only). The original `DEVIATIONS-LOG.md` is **frozen** at Phase-0 and holds the D/F/S/M rows this document cites by number. Context is summarised inline; the logs have full history.

## 1. Credentials, secrets, and rotation

### 1.1 Source of truth

**1Password is the canonical source of truth.** The OpenClaw vault on the EU shard (`https://my.1password.eu`) holds every credential the system depends on, plus session-closure-archive Notes for each major operational session. As of 2026-06-02 (6a.33 probe) the vault contains 35 items.

The Mini's local config files (`openclaw.json`, `secrets.json`, the `credentials/` directory) hold operational copies of credentials in formats the OpenClaw runtime can read. The local copies are derived from 1Password — if any local file is lost or corrupted, the recovery path is "fetch from 1Password and re-materialise."

Op CLI access requires authentication per shell session: `op whoami` confirms an active session; `eval $(op signin)` re-authenticates. Sessions persist for ~30 minutes of activity; longer idle gaps require re-signin.

### 1.2 The SecretRef resolution chain

`openclaw.json` holds **references** to secrets (never plaintext values). Three resolution surfaces are active:

1. **`secrets.json` resolution.** The default provider, configured in `openclaw.json` as `secrets.providers.default = {source: file, path: ~/.openclaw/secrets.json, mode: json}`. Plugins reference this via SecretRef — for example, `plugins.entries.brave.config.apiKey` and `models.providers.openai.apiKey` are SecretRefs that resolve to `secrets.json` keys (`BRAVE_API_KEY` and `OPENAI_API_KEY` respectively).
2. **Auth-profiles inheritance.** Per-agent `agents/<id>/agent/auth-profiles.json` (mode 0600 openclaw:staff) holds the resolved provider keys — confirmed across main + 9 agents (6a.32/6a.33 sanity): each carries an `anthropic:default` profile (the default LLM provider) and an `openai:default` profile. **F61 — the embedding credential lives here, not in `secrets.json`:** the embedding subsystem reads the per-agent `openai:default` profile, so `secrets.json`'s `OPENAI_API_KEY` alone does not satisfy embeddings. Any agent created via `openclaw agents add` (6b/6c) inherits only `anthropic:default` and needs `openai:default` provisioned before its first embedding call. Auth-profiles are **materialise-once, not live** (F25): a rotation here must update each per-agent copy explicitly, or live API/embedding calls return 401.
3. **Filesystem credentials.** Larger or more complex credential blobs live as files in `~/.openclaw/credentials/` — Google OAuth client secret, Calendar refresh token, Telegram pairing whitelist. These are referenced by path from openclaw.json or by the gog wrapper directly.

The 1Password layer sits above all three: every credential should have a corresponding Secure Note. Filesystem credentials are backed up after rotation; SecretRef-resolved keys are fetched fresh from 1Password when rotating.

### 1.3 Per-credential inventory

| Credential | Storage | Used by | Rotation cadence |
|------------|---------|---------|------------------|
| `OPENAI_API_KEY` | `secrets.json` + 1Password backup | OpenAI plugin (fallback inference). NB: embeddings use the per-agent `openai:default` auth-profile, not this key (D22 closed; F61, §1.2) | Annual or on compromise |
| `BRAVE_API_KEY` | `secrets.json` + 1Password backup | Brave plugin (web search) | Annual or on compromise |
| Anthropic API key | Auth-profiles (exact resolution path documented in OpenClaw per-version docs; confirm at next operational session) | Default LLM provider | Annual or on compromise |
| Per-bot Telegram tokens (4: pa/pro/cos/leo) | **Inline** in `openclaw.json` under `channels.telegram.accounts` (~46 chars each); 1Password Secure Notes as backup. SecretRef refactor deferred (Option 3 / step 16, F72) | Telegram channel routing | Only on compromise (bot tokens are durable) |
| Telegram pairing whitelist | `~/.openclaw/credentials/telegram-pa-allowFrom.json` (57 bytes) + 1Password backup | Inbound message filter for PA | Manual rebuild on whitelist change |
| Gateway auth token | 1Password Secure Note | SSH-side device pairing (`openclaw devices list` and operator scopes) | On operator change or compromise |
| Google OAuth client secret | `~/.openclaw/credentials/gog-client-secret.json` (400 bytes) | gog wrapper (Gmail, Calendar) | On scope change or compromise |
| Google keyring password | `~/.openclaw/credentials/gog-keyring-password` (45 bytes) + env var `GOG_KEYRING_PASSWORD` (44 chars) | Unlocks stored Google refresh tokens | Annual or with client-secret rotation |
| Calendar refresh token | `~/.openclaw/credentials/calendar-backup-token.json` (746 bytes) | Calendar backup pipeline | 7-day TTL (External/Testing OAuth app); refresh procedure in §2.3 |

**F44 current state (CLOSED-AS-DESCOPED, D24).** The calendar-backup launchd job is descoped — its plist is parked at `ai.openclaw.calendar-backup.plist.disabled-D24` and is not loaded (verified: `Could not find service … gui:502`, durable through reboot). With no caller, the token's 7-day TTL is moot; F44 does not bite while the job is descoped. It resurfaces only when calendar-backup is re-enabled (targeted ~2026-07-23±, at Phase G / 6b+ — **date/status unverified at 6c.31; confirm at 6d**) — and re-enable has a hard precondition: the SR-2 manual ICS export (see §2.12 and the session opener). The Production-OAuth move (the OAuth app Internal/Production flip that removes the 7-day TTL) is part of that re-enable, not a near-term step — **verify at 6d**.

### 1.4 Rotation procedure (general pattern)

Six steps, applied to any rotation. **Order matters — do not revoke the old credential (step 6) until verification (step 5) passes, or you can lock yourself out.**

1. **Generate the new credential** at the source provider (OpenAI dashboard, Brave dashboard, Telegram BotFather, etc.).
2. **Backup to 1Password first.** Create or update the relevant Secure Note. Verify presence (not value): `op item get <id> --fields=<field> --format=json | jq -r '.value | length'` returns the expected byte length.
3. **Update local storage.** For `secrets.json`-resolved keys: edit `secrets.json` using the F17 staging pattern — write to `secrets.json.new` in the same directory, then `mv secrets.json.new secrets.json` (rename-only mv preserves permissions). Verify mode 0600 openclaw:staff with `ls -la`. **Never `mv` from `/tmp`** — macOS regresses permissions on tmp-rooted moves (F17 lineage).
4. **Restart-or-not check.** Hot-reload vs restart depends on what changed:
   - Sub-key changes within an existing block: hot-reload (gateway picks up within ~30s).
   - First-time creation of a top-level config block: restart required (§2.1).
   - Auth-profile changes: explicit re-materialise required (per F25 — restart alone won't refresh auth-profile-resolved credentials).
   When in doubt, check `~/.openclaw/logs/gateway.log` for the "config reloaded" message; if absent within 30s after the change, restart via §2.1.
5. **Verify resolution.** Run a small read operation that uses the rotated credential. For OPENAI: `openclaw infer --provider openai --model gpt-4o "test"`. For Anthropic: same with anthropic provider. For Telegram: send a test message to the bot and confirm the agent responds.
6. **Revoke the old credential** at the source provider — only after step 5 passes.

Per-credential specifics live as operational runbook notes in 1Password. When a credential is added or rotated, update or create the corresponding Note in the same op session — drift between live state and 1Password backup is operational debt.

### 1.5 Op CLI hygiene (F51 / F52)

Two standing hygiene rules:

**F51 — op verbs touching credential material.** Never let credential bytes appear on the terminal where they might end up in scrollback, screen recording, or chat-layer auto-linkification. Pattern:

```bash
# Verify presence without echoing value:
op read "op://OpenClaw/<note>/<field>" --no-newline > /dev/null && echo "ok"
op item get <id> --fields=<field> --format=json | jq -r '.value | length'
```

Use `--no-newline` on `op read` — without it, op appends a trailing newline and any subsequent byte-length or sha computation will be off by one. Same with `op read --out-file`: strips the trailing newline.

**F52 — `op://` URI restrictions.** ASCII-only in URI references. Hyphens, not em-dashes. Human-readable Note titles can use em-dashes for fancy typography, but URI-referenceable identifiers must be pure ASCII to avoid op CLI rejection.

**Session-expiry pre-flight.** Every shell that uses op CLI starts with `op whoami` as a pre-flight check. If expired, `eval $(op signin)` re-authenticates.

### 1.6 What not to do

Hard rules — violations are immediate stop-and-rollback events:

- **Never echo credential bytes** to chat, shared logs, or scrollback-visible terminals. Use sha12 + byte-length as the verification proxy. The chat layer auto-linkifies any dotted-TLD string (F14 v2 lineage); credential paths in raw form can be transformed unpredictably.
- **Never `mv` credentials from `/tmp`** — permissions regress (F17). Stage in the target's directory: `~/.openclaw/secrets.json.new` then `mv secrets.json.new secrets.json`.
- **Never commit `secrets.json`, `credentials/*`, or `agents/*/agent/auth-profiles.json` to git.** The config repo (F72, §2.11) is protected by a **default-deny** `.gitignore` (`*` plus a tight allowlist) and tracks only the *sanitised* config — never live secrets; the sanitiser strips the inline Telegram tokens to placeholders before any commit. There is no automatic secret-scanning push pipeline — the 4am launchd job is the **vault-backup** (Obsidian → Google Drive, §2.9), unrelated to config secrets. If you ever widen the allowlist, re-run the pre-push leaked-secret scan (§2.11) first. Workspace files only — credentials never.
- **Never share an op session across users.** Op session tokens are user-bound; a `pfedchenkov`-authenticated session is not accessible from the `openclaw` shell. Re-signin in each shell where op is needed.
- **Never rotate Auth-profiles credentials without re-materialise step** (F25). Restart alone does not refresh them; live API calls return 401 until materialise runs.

## 2. Failure modes + recovery procedures

### 2.0 Symptom → procedure mapping

| Symptom | First check | Section |
|---------|-------------|---------|
| `curl http://127.0.0.1:18789/healthz` fails or hangs | `launchctl list \| grep openclaw` | §2.1 |
| Mac Mini was rebooted / powered down | gateway should auto-recover via `KeepAlive` — verify, don't act | §2.1 |
| Agent replies with API auth error (401) | Identify which credential surface owns the failing call | §2.2 |
| Calendar backup script fails with token-expired | `stat -f "%Sm" ~/.openclaw/credentials/calendar-backup-token.json` | §2.3 |
| Gmail/Calendar gog operations fail with scope or token errors | `gog auth list` | §2.4 |
| `openclaw memory search` returns no results or errors | `openclaw memory status` for the relevant agent | §2.5 |
| Telegram message not reaching the agent | `ls ~/.openclaw/credentials/telegram-pa-allowFrom.json` + gateway log scan | §2.6 |
| Config file sha12 differs from DEVIATIONS-LOG anchor | Compare contents before any recovery action | §2.7 |
| Vault-backup didn't run / vault not on gdrive | `launchctl print …/ai.openclaw.vault-backup \| head -3` + log-tail | §2.9 |
| Need an encrypted 1Password snapshot | run op-backup interactively | §2.10 |
| Config repo stale after an `openclaw.json` change | re-run the sanitiser + commit + push | §2.11 |
| Re-enabling a descoped job (calendar-backup / security-audit) | rename `.disabled-D24` → `.plist`, then bootstrap (GUI) | §2.12 |
| Browser tool fails "Playwright is not available …" (esp. after `openclaw update`) | `ls -l …/openclaw/node_modules/playwright-core` (P6-40 symlink) | §2.13 |
| Sandboxes/containers down; "Cannot connect to the Docker daemon" | `colima status` + colima-socket-watchdog log | §2.15 |
| No heartbeat / beats stalled / multi-hour sawtooth gaps | `openclaw system heartbeat status`; watchdog log | §2.14, §2.15 |
| Beats firing overnight (should be quiet 23:00–07:00) | `ls ~/.openclaw/logs/heartbeat.night`; nightwindow log | §2.15 |
| Marie goes quiet / stuck after a long chat; a `:main` ballooned | chat-bloat-reset / session-freshstart logs | §2.15 |
| Daily spend/cost note not arriving, or a spend spike | `cost-notify` logs (Console is authoritative) | §2.15 |
| memory-health canary alarm, or memory metrics regressed | `tail ~/.openclaw/state/memory-health/daily.log` | §2.15 |
| Awaited Gmail reply not surfaced | `tail ~/.openclaw/logs/reply-watcher.log` | §2.15 |
| Agent credential-save request stuck in a queue | `ls ~/.openclaw/workspace-*/.cred-queue/pending` | §2.15 |
| Need to fill a generated password / card into a live form | host-only autofill CLIs (agent paused) | §2.16 |
| Something feels off but no clear symptom | Run the catch-all health check | §2.7 |

### 2.1 Gateway restoration (F45 ceremony)

The gateway is `launchd`-supervised. Most failures are recovered automatically by `KeepAlive`. The procedure below is for cases where automatic recovery has not kicked in — typically plist corruption, persistent crash loops, or post-update misconfiguration.

**Step 1 — Diagnose.** Confirm the gateway is actually down (not just slow):

```bash
launchctl list | grep openclaw                            # is it registered?
curl --max-time 5 http://127.0.0.1:18789/healthz          # is it responding?
pgrep -fl 'openclaw.*gateway'                             # is the process alive?
tail -50 ~/.openclaw/logs/gateway.err.log                 # what crashed it?
```

If `launchctl list` shows the entry but the process is dead and not respawning, the plist is registered but the executable is failing repeatedly. Read the err log for the cause before any restart attempt.

**Step 2 — Verify plist integrity.** Compare against the canonical anchor (`~/Library/LaunchAgents/ai.openclaw.gateway.plist`, ~2309 bytes, sha12 lineage tracked in DEVIATIONS-LOG.md):

```bash
plutil -lint ~/Library/LaunchAgents/ai.openclaw.gateway.plist
stat -f "size=%z" ~/Library/LaunchAgents/ai.openclaw.gateway.plist
shasum -a 256 ~/Library/LaunchAgents/ai.openclaw.gateway.plist | cut -c1-12
```

If `plutil -lint` errors, the plist is corrupted. Restore from 1Password backup: the canonical plist content is stored as a Secure Note in the OpenClaw vault. Stage the restored content as `…/ai.openclaw.gateway.plist.new` in the same directory, then `mv` (rename-only, preserves permissions). Verify mode 0644 owned by the operating user.

**Step 3 — Re-bootstrap.** This step requires a **GUI session on the openclaw account** (per D20 / F55 — `gui/<uid>` launchd domains are GUI-session-bound on macOS). In current Phase 0 setup, that means Screen Sharing from the admin MacBook. Without GUI access, the commands return errors that look successful but do not actually re-bootstrap the agent.

```bash
launchctl bootout gui/$(id -u) ~/Library/LaunchAgents/ai.openclaw.gateway.plist
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/ai.openclaw.gateway.plist
```

**Step 4 — Verify.** Within ~10 seconds:

```bash
curl --max-time 5 http://127.0.0.1:18789/healthz          # expect HTTP 200 + ok:true
launchctl list | grep openclaw                            # expect entry with PID
openclaw doctor --non-interactive --no-workspace-suggestions   # full health sweep
```

If healthz still fails after restart, escalate — do not retry the bootout/bootstrap cycle; you may be making things worse. Read the err log, capture the failure mode, and surface to Peter before further action.

**Post-reboot recovery (expected path).** A full Mini reboot or power-cycle needs **no manual action** — the `KeepAlive` LaunchAgent respawns the gateway automatically. Empirically confirmed 2026-05-17 (operator-initiated verification reboot, 6a.27): after `kern.boottime`, the gateway returned on a new PID with `healthz:200`, correct `OPENCLAW_SERVICE_VERSION`, and the plist unchanged (2309 / mtime intact). After any reboot, **verify, do not restart**:

```bash
uptime                                                    # load is briefly high post-boot; let it settle
pgrep -fl openclaw-gateway                                # new PID expected — not drift
curl --max-time 5 http://127.0.0.1:18789/healthz          # expect HTTP 200
launchctl print gui/$(id -u)/ai.openclaw.gateway 2>/dev/null | grep -E 'state =|pid ='
```

A changed gateway PID after a reboot is expected (KeepAlive respawn), not state drift — do not re-baseline anything except the PID. If, and only if, the gateway is **not** back after a reboot, fall through to Steps 1–4 above (diagnose → plist integrity → re-bootstrap → verify). The reinstall path `openclaw gateway install --force --port 18789 && openclaw gateway start` is the last resort only — it rewrites the plist (mtime becomes "now"; update any mtime anchor). Never use reinstall as a routine restart.

**Cold-boot recovery needs a manual login (P6-43, decided 6c.8 — accepted gap).** The "no manual action" path above holds only for a **warm** reboot where the disk is already unlocked / the user GUI session reaches login. On a **cold boot from power-off**, FileVault is **ON** and macOS auto-login is **OFF** (deliberately — auto-login would defeat FileVault; we are not trading away disk encryption). The Mini therefore halts at the **FileVault unlock screen**, the `gui/502` LaunchAgents (gateway, Colima, watchdog) do **not** start, and the box is unreachable over the network (no user session yet) — KeepAlive cannot help because the user domain isn't loaded. **Recovery is unavoidably physical/manual:** unlock at the console — in person, or via **Screen Sharing if the "login window" (pre-auth) VNC path is reachable** — entering the account password (FileVault Recovery Key in 1Password "OpenClaw" as the fallback). Once logged in, the whole `gui/502` stack autostarts and recovers on its own; then **verify, don't restart** (above). Expected frequency ~1–2×/month (unplanned power loss); no auto-login, no uptime monitor (the box is unreachable at the FileVault screen regardless, so a monitor would only restate the obvious). This is an accepted operational gap until/unless Andreas owns a different posture.

### 2.2 Token-expired (general pattern)

When an agent's API call returns 401 or "token expired":

1. **Identify the credential surface.** Which API failed? (Anthropic / OpenAI / Brave / Google / Telegram.) Each lives in a different surface (§1.2).
2. **Verify the local value matches 1Password.** Don't echo the value; compare byte-length and sha12 via the F51 hygiene pattern (§1.5).
3. **If local value matches 1Password:** the upstream credential itself is expired or revoked. Follow §1.4 rotation procedure (generate new → backup → update local → re-materialise if auth-profile → verify).
4. **If local value differs from 1Password:** the local copy has drifted (someone or something modified it). Restore from 1Password using the F17 staging pattern, then re-materialise.
5. **Always include the re-materialise step for auth-profile-resolved credentials** (F25). Gateway restart alone does not refresh them.

### 2.3 F44 — Calendar backup token refresh

Specific to `~/.openclaw/credentials/calendar-backup-token.json`. **Reference procedure — not live while calendar-backup is descoped (D24, §2.12).** The token has a 7-day TTL because the underlying Google OAuth app is in External/Testing status; the Production-status move (part of the Phase G re-enable, ~2026-07-23±) eliminates the 7-day window. When the job is live again, refresh it as follows:

```bash
# Check age
stat -f "%Sm" -t "%Y-%m-%dT%H:%M:%S" ~/.openclaw/credentials/calendar-backup-token.json
```

Refresh requires re-running the calendar backup OAuth flow (specific verb documented in the operational runbook Note in 1Password; procedure requires browser interaction from the pfedchenkov user side). Cannot be done SSH-only. After refresh, verify the new mtime and sha12 differ from prior, and update the 1Password backup of the token file.

### 2.4 gog (Google) re-grant

When Gmail or Calendar operations fail with scope or token errors:

```bash
gog auth list                                # which accounts are registered?
gog auth status pfedchen@gmail.com           # is the token valid?
```

If a re-grant is needed:

1. Run `gog auth login <email>` to start the OAuth flow.
2. Browser interaction from the pfedchenkov user side (Screen Sharing required).
3. Approve scopes; tokens are stored in the gog keyring at `~/.openclaw/credentials/` with the keyring password (env var `GOG_KEYRING_PASSWORD`).
4. Verify: `gog gmail list --max 1` or `gog calendar events list --max 1`.

Note: 8 keyring files currently exist with mixed schema state (per F46). Don't bulk-delete them — work additively. If gog auth list shows an unexpected entry, leave it alone unless explicitly removing.

### 2.5 Memory + sessions recovery

When `openclaw memory search` returns nothing or errors:

```bash
openclaw memory status                       # per-agent index + provider state
openclaw memory status --json                # machine-readable
```

Interpretation:

- **`dirty: true` + `files: 0`** — pre-build state for that agent (no workspace authored yet). Not a fault. Expected for all 9 agents until their build sessions land.
- **`dirty: true` + `files: N` (N > 0)** — index is stale; rebuild with `openclaw memory index --force` for the affected agent.
- **Provider error in status output** — embedding API auth issue. D22 is **closed**: OpenAI `text-embedding-3-small` is the live embedding provider across all 9 agents (`provider==openai`, vector+fts available — verified 6a.33/6a.34), `fallback:"none"`. The embedding credential is **not** `secrets.json`'s `OPENAI_API_KEY` — the subsystem reads the per-agent `openai:default` profile in `agents/<id>/agent/auth-profiles.json` (F61). A provider auth error therefore means that per-agent profile is missing or stale (provision per §1.2), not `secrets.json`. Do **not** set `memorySearch.provider` to `local` — it is schema-valid but has no runtime adapter and hard-throws (S6).
- **`vector: not ready`** — `sqlite-vec` extension missing or wrong architecture. Reinstall path: `npm reinstall openclaw` (touches a lot; surface to Peter before doing).

`openclaw memory index --force` is the only routinely-safe state-changing memory verb. Promote and rem-backfill verbs mutate `MEMORY.md` and `DREAMS.md` respectively — don't run them as recovery without understanding the consequences.

For the session store (distinct from memory — see STATE-OF-NATION §2.5): `openclaw sessions --agent <id>` lists a single agent's sessions; `openclaw sessions --all-agents` aggregates. `openclaw sessions cleanup` is the only state-changing sessions verb — pause before running. Orphan `*.trajectory.jsonl` files are cosmetic (F56) and not enumerated by the sessions CLI; doctor `--fix` cleans them.

### 2.6 Telegram pairing recovery

The pairing whitelist for PA lives at `~/.openclaw/credentials/telegram-pa-allowFrom.json` (57 bytes, sha12 anchor `87d2fab7d842`). If lost or corrupted:

1. Fetch the canonical content from 1Password (`telegram-pa-allowFrom` Secure Note).
2. Stage as `telegram-pa-allowFrom.json.new` in the same directory.
3. `mv` to final filename, verify mode 0600 openclaw:staff, verify sha12 matches the canonical anchor.
4. Gateway hot-reloads the credential within ~30s; verify by sending a test message from the paired Telegram account.

As of 6a.34, 4 Telegram bots are configured under `channels.telegram.accounts` (all `enabled`, `dmPolicy: pairing`): **pa = Marie, pro = Charlotte, cos = Andreas, leo = Sophie** (plus an empty `default`). Only the PA bot (Marie) is paired so far; the other three pair at their respective build sessions (6b/6c), each gaining its own `allowFrom` file following the same pattern. The 4 bot tokens are stored **inline** in `openclaw.json` (see §1.3), not via SecretRef — that refactor is deferred (Option 3 / step 16, F72).

### 2.7 Config drift + general health check (catch-all)

**Config drift.** If a config file's sha12 differs from the anchor recorded in the deviations log: do not assume corruption — it could be a legitimate edit from a prior session. Compare contents (`diff` against the 1Password-backed reference, or against the prior session's archived copy), establish whether the change is intentional, *before* any recovery action. Only restore-from-1Password if the change is confirmed unintended. **Current live `openclaw.json` anchor (6c.31):** sha12 `3267edd8b6aa`, 10489 bytes, mode 0600, A-7 clean (pin 2026.4.22).

**`openclaw.json` editing discipline (F17).** Never edit `openclaw.json` in place. Stage a full `openclaw.json.new` in the **same directory**, then atomic rename-only `mv` (same-dir — never from `/tmp`), always paired with `chmod 600 && chgrp staff` in the same block; keep `openclaw.json.last-good` byte-identical after a clean apply (A-7). Note the ops-tooling guard: `jq` / `mv` / `cp` targeting `openclaw.json` are **Bash-deny-listed** in this Claude-Code harness, so config edits are prepared as a staged diff and **surfaced for Peter to apply** (or applied by an explicitly-authorised script) — never routed around the deny-list. Any change to `agents.defaults.sandbox` must be mirrored into the per-agent `pa`/`pro` sandbox blocks (P6-24). After any live `openclaw.json` change, refresh the sanitised snapshot (§2.11).

**General health sweep.** When nothing is obviously broken but something feels off, run the read-only sweep:

```bash
openclaw doctor --non-interactive --no-workspace-suggestions
```

The `--non-interactive` flag is mandatory (per L5 / F20): unflagged doctor is interactive-blocking even when piped through `head`. The `--no-workspace-suggestions` flag suppresses Phase-0-noise about absent workspaces.

Doctor output covers: gateway state, plist sanity, session store entries, orphan transcript files, credential file presence, plugin loads, allowlist coverage. Anything actionable is flagged with `--fix` hints. The `--fix` flag is the only state-changing flag for safe repairs; `--force` is aggressive and should not be run without understanding what it does.

### 2.8 When to call Peter vs act autonomously

**Act autonomously** (no escalation needed):

- Read-only health checks (`doctor --non-interactive`, `memory status`, `sessions --agent <id>`).
- Memory index rebuilds (`memory index --force`) on an agent whose store is empirically stale.
- Colima sandbox restarts (`colima start` after `colima stop`).
- Doctor `--fix` for clearly cosmetic issues (orphan transcript files per F56, etc.).

**Wait for Peter** (always):

- Any edit to `openclaw.json`, `secrets.json`, or `credentials/*`. Even when the change seems clearly correct.
- Any credential rotation (no matter how routine).
- Any plist modification or launchd bootstrap operation.
- Anything where the gateway is down and basic restart doesn't recover.
- Any 1Password authentication failure or vault-access issue. If 1Password is compromised, everything downstream is suspect.
- Anything touching workspace files (workspaces are owned by Peter's build sessions).

When in doubt: read-only investigation only; surface findings; wait for direction.

### 2.9 Vault-backup (Obsidian → Google Drive, scheduled)

The Obsidian vault is backed up off-Mini daily. This is a **scheduled maintenance job**, not an incident procedure — documented here so you can verify it and run it by hand.

- **What runs:** `~/.openclaw/scripts/vault-backup.sh` at 04:00 daily, via the LaunchAgent `ai.openclaw.vault-backup.plist` (1122 B, loaded in `gui/502`). It `rclone sync`s `~/obsidian-vault` → `gdrive-personal:Vault/` and writes a dated archive copy to `gdrive-personal:Vault-Archive/YYYY-MM-DD/`. The rclone remote is scope-confined.
- **Verify a clean fire** (the A-1 rule — confirm what it *did*, not just that it loaded; F69 head-3, never head-1):

```bash
launchctl print gui/$(id -u)/ai.openclaw.vault-backup 2>&1 | head -3
launchctl print gui/$(id -u)/ai.openclaw.vault-backup 2>&1 \
  | grep -E '^[[:space:]]*(state|last exit code|run count)'
tail -20 ~/.openclaw/logs/vault-backup.log
```

Expect: top-level `state = not running` (idle between fires), `last exit code = 0`, and a log block ending `vault-backup END (rclone exit=0)`. **`run count` is absent in this launchd build** — a known quirk, not a fault; trust `last exit code = 0` + the log-tail. Nested `state = active` lines are endpoint sub-states — ignore them.
- **Run by hand:** `bash ~/.openclaw/scripts/vault-backup.sh` (idempotent `rclone sync` — a no-op if nothing changed).
- **If it failed** (`last exit code != 0`, or a non-zero rclone exit in the log): read the log tail for the rclone error. Common causes are an expired gdrive OAuth token (re-grant) or a network blip (re-run by hand). Don't touch the plist to "fix" a transient failure.
- **F68 PATH note:** the job resolves `rclone` at `/opt/homebrew/bin/rclone`. launchd does not inherit your interactive PATH, so any launchd job touching a Homebrew binary must carry `/opt/homebrew/bin` on PATH (this job already does). Apply the same `EnvironmentVariables` PATH prophylactic to any *new* launchd job (§2.12).

### 2.10 Op-backup (encrypted 1Password snapshot)

A periodic encrypted export of the OpenClaw 1Password vault, kept on-Mini as a belt-and-braces copy.

- **What it is:** `~/.openclaw/scripts/op-backup.sh` produces an **OpenSSL AES-256-CBC** blob (PBKDF2 key derivation, salted); the latest is symlinked at `~/.openclaw/backups/onepassword-vault-latest.enc` → `onepassword-vault-YYYY-MM-DD-HHMMSS.enc` (mode 0600). The decryption passphrase lives at `~/.openclaw/credentials/op-backup-passphrase` (mode 0600, ≥20 B) and is stored **three ways** (on-Mini file + 1Password + offline) so loss of any one path is survivable.
- **Status:** run **interactively** at meaningful checkpoints. The F70 blocker (no unattended `op` auth under launchd) is now **resolved** — a 1Password **service account** exists and the `op-get`/`op-put` wrappers (§2.16) use it — so an unattended op-backup is now mechanically possible. But **no `ai.openclaw.op-backup` timer exists in the fleet as of 6c.31**: whether to schedule it (vs. keep it a deliberate manual checkpoint) is a **6d decision — verify/decide at 6d**, don't assume it's automated. It does not currently run on a timer.
- **Run by hand:** from a shell with an authed `op` session (`op whoami` first), execute `op-backup.sh`. It reads the passphrase file and writes a fresh dated `.enc` + repoints the symlink.
- **Verify** (F59 — never read the passphrase or the blob contents; structure only):

```bash
ls -l ~/.openclaw/backups/onepassword-vault-latest.enc
stat -f "%z" "$(readlink -f ~/.openclaw/backups/onepassword-vault-latest.enc)"   # expect > 10 KB
stat -f "%Sp" ~/.openclaw/credentials/op-backup-passphrase                       # expect -rw-------
```

- **Restore:** with `ENC` = the resolved `onepassword-vault-latest.enc` target and `PASS` = `~/.openclaw/credentials/op-backup-passphrase`:

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -in "$ENC" -pass file:"$PASS"
```

That emits the decrypted JSON blob. Recovery-of-last-resort if 1Password itself is unreachable; normally restore straight from 1Password.

### 2.11 Config repo — backup, restore, and snapshot refresh (F72)

`~/.openclaw` is a git repo pushed to the **private** GitHub repo `pfedchen-openclaw/openclaw-config` (root commit `2067ad2`). It is the DR layer for config + scripts + launchagents (spec §4.9/§4.11, built 6a.33).

- **What's tracked (sanitised only):** a default-deny `.gitignore` (`*` + tight allowlist) tracks exactly `.gitignore`, `README.md`, `openclaw.sanitised.json`, `scripts/{vault-backup.sh, op-backup.sh, calendar-backup.py, sanitise_openclaw.py}`, and `launchagents/` (the 4 plist copies). **No** live `openclaw.json`, `secrets.json`, `credentials/`, `auth-profiles`, or `.enc` — confirmed by a pre-push leaked-secret scan + browser check (C4/C5).
- **The sanitised snapshot drifts.** `openclaw.sanitised.json` is `openclaw.json` with the 4 inline Telegram tokens replaced by `__REDACTED__`. After **any** change to live `openclaw.json` — a 6b agent build, a token rotation, a channel edit — refresh it:

```bash
op whoami
python3 ~/.openclaw/scripts/sanitise_openclaw.py --check    # dry-run: structural match
python3 ~/.openclaw/scripts/sanitise_openclaw.py --write    # write openclaw.sanitised.json
python3 ~/.openclaw/scripts/sanitise_openclaw.py --verify   # match True, 0 token-shaped strings
git -C ~/.openclaw add openclaw.sanitised.json
git -C ~/.openclaw commit -m "refresh sanitised snapshot"
git -C ~/.openclaw push
```

Skip this and the repo describes a stale config. (This duty moves to the CoS at 6c per the F72 note.)
- **Pre-push gate** (any time you stage more than the existing allowlist): re-run the leaked-secret scan over the staged tree and confirm `--verify` shows 0 token-shaped strings before pushing. In doubt, don't push — surface to Peter.
- **Restore on new hardware:** clone the repo for *structure* + scripts, but the clone is **sanitised** — live secrets come from **1Password**, not git. Path: clone → re-materialise `secrets.json` + `credentials/` + per-agent `auth-profiles.json` from 1Password → place the live `openclaw.json` (real tokens) → verify. The README carries the steps. (A full cross-hardware DR dry-run is still pending — S5, post-6c.)
- **Eventual end-state (deferred):** Option 3 / step 16 refactors the 4 inline tokens to SecretRefs, after which `openclaw.json` itself becomes trackable and the sanitiser retires.

### 2.12 Re-enabling a descoped launchd job (D24)

Two jobs are intentionally descoped and parked with a non-`*.plist` suffix so a GUI login can't silently re-load them (F67): `ai.openclaw.calendar-backup.plist.disabled-D24` and `ai.openclaw.security-audit.plist.disabled-D24`. To bring one back:

1. **Preconditions first.**
   - **calendar-backup:** discharge **SR-2** before re-enabling — Peter's manual ICS export to a 1Password Secure Note ("Calendar — frozen baseline snapshot YYYY-MM-DD"), the pre-write safety floor. Then refresh the OAuth token (§2.3) and plan the Production-OAuth move (closes F44).
   - **security-audit:** its original failure was the **F68 PATH gap** — launchd's minimal PATH lacked `/opt/homebrew/bin`, so `node` wasn't found and it failed silently every fire. Before re-enabling, add an `EnvironmentVariables` PATH (e.g. `/opt/homebrew/bin:/usr/bin:/bin:/usr/sbin:/sbin`) to the plist, or the same silent failure recurs.
2. **Rename back:** stage in place — `mv ai.openclaw.<job>.plist.disabled-D24 ai.openclaw.<job>.plist`. Verify mode 0644 and `plutil -lint`.
3. **Bootstrap (GUI session required, D20 — over SSH this exits 125; use Screen Sharing):**

```bash
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/ai.openclaw.<job>.plist
```

4. **Verify what it DOES, not just that it loaded** (A-1): `launchctl print … | head -3` (F69, never head-1) for loaded state, then the job's log-tail + output-existence after its first fire. A job that is "loaded" but failing silently is the exact F68/F63 trap.

### 2.13 Host browser-dep repair (P6-40)

**Symptom:** the host `browser` tool fails with *"Playwright is not available in this gateway build; … install playwright-core, then restart the gateway."* — most often immediately after an `openclaw update` / reinstall, which wipes the symlink.

**Root cause (P6-40):** `playwright-core@1.59.1` ships bundled only at `…/openclaw/dist/extensions/browser/node_modules/playwright-core`, but the gateway importer sits at `dist/` level and Node's module walk-up never reaches it. The 6c.6 fix is a symlink from pkg-root `node_modules` to that bundled copy; the gateway **memoises the failed import**, so a restart is mandatory after re-linking.

**One-touch repair:** run `~/.openclaw/scripts/browser-dep-repair.sh` (SSH-safe — gateway restart only, no GUI/D20 needed). It re-creates the symlink idempotently, restarts the gateway only if the link changed, and verifies with a real `navigate`. Exit 0 = healthy. **Run it after every `openclaw update`.** A second run is a clean no-op (no restart) when already healthy.

**Verify by hand:** `openclaw browser navigate https://example.com` → "navigated to https://example.com/".

### 2.14 Heartbeat pause / resume — cost-bleed stop (P6-77)

**When:** the pair is degraded (e.g. the Anthropic cap is hit, so every turn fails over to paid `openai/gpt-5`, and/or Google OAuth is expired) and you want to stop the autonomous spend **without** taking the gateway offline. The metronome is the heartbeat scheduler, not inbound messages: Marie `pa` beats every 30m + Charlotte `pro` every 4h ≈ 54 turns/day, and a beat costs even when `silent:true` (cognition runs regardless).

**Pause (SSH-safe — runtime only; no gateway restart, no `openclaw.json` change, D20 N/A):**
1. `touch ~/.openclaw/logs/heartbeat.paused` (drop a dated reason inside — the convention).
2. `openclaw system heartbeat disable` → expect `{ok:true, enabled:false}`.

While the sentinel exists, the `heartbeat-watchdog.sh` PAUSE guard (added P6-77) **re-asserts** the disable every 15m — so a KeepAlive/reboot restart can't silently revive the in-memory disable — and **skips** its stall-restart + fallback-alert (no churn, no Telegram spam).

3. **Verify:** no new `[heartbeat]` line in `logs/gateway.log` after the next scheduled `:27`/`:57`; `tail logs/heartbeat-watchdog.log` shows `PAUSED (sentinel present)`.

**Resume (ORDER MATTERS):**
1. `rm ~/.openclaw/logs/heartbeat.paused` — **first**, or the watchdog re-disables within 15m.
2. `openclaw system heartbeat enable`.
3. **Verify:** a real beat fires (`[heartbeat]` in gateway.log within one interval).

The watchdog PAUSE guard began as a **temporary** P6-77 patch; the WS-8/9 alerting redesign has since **landed** (6c.11, [P6-71]/[P6-73]) and the guard is folded into `heartbeat-watchdog.sh` alongside it (episode-throttled fallback/blocker/cost alerting) — see §2.15. This is a runtime pause; the config-level heartbeat/fallback redesign is separate (verify final shape at 6d).

### 2.15 LaunchAgent fleet — full inventory + per-job operations

The fleet grew from 2 active jobs (6a.34) to **11 live** `ai.openclaw.*` jobs (6c.31) plus the 2 descoped `.disabled-D24` plists. All are user-domain `gui/502` LaunchAgents — every bootstrap/bootout is GUI-session-bound (D20 — over SSH they exit 125; use Screen Sharing). For every job the A-1 rule holds: **verify what it DID, not just that it loaded** (F69 `head -3`, never `head -1`).

| Job (`ai.openclaw.*`) | Trigger | What it does | Script | § |
|---|---|---|---|---|
| `gateway` | KeepAlive + RunAtLoad | gateway supervisor | (built-in) | §2.1 |
| `colima` | RunAtLoad, **KeepAlive=false** | starts the Colima container runtime at login | `colima start` | §2.15 |
| `colima-socket-watchdog` | every 5m | probes docker socket; restarts Colima on a wedged socket | `colima-socket-watchdog.sh` | §2.15 |
| `heartbeat-watchdog` | every 15m | beat-stall restart + WS-8 alerting + intra-day `:main` reset net | `heartbeat-watchdog.sh` | §2.14/§2.15 |
| `heartbeat-nightwindow` | 23:00 + 07:00 | suppress/restore overnight beats | `heartbeat-nightwindow.sh` | §2.15 |
| `session-freshstart` | 06:55 daily | archive+clear `:main` for a lean morning session | `session-freshstart.sh` | §2.15 |
| `chat-bloat-reset` | every 15m | self-heal Marie's `:telegram:direct` chat when bloated+idle | `chat-bloat-reset.sh` | §2.15 |
| `memory-health` | 23:55 daily + RunAtLoad | Memory-v2 acceptance-gate canary | `memory-health.py` | §2.15 |
| `cost-notify` | every 3m | per-task token+$ push; daily total from Console | `cost-notify.py` | §2.15 |
| `reply-watcher` | every 15m | bounded Gmail poll → surface awaited replies (dryrun default) | `reply-watcher.sh` | §2.15 |
| `cred-save-watch` | every 45s | drain the per-agent credential-save queues | `cred-save-drain.sh` | §2.15 |
| `vault-backup` | 04:00 daily | Obsidian → Google Drive backup | `vault-backup.sh` | §2.9 |
| `calendar-backup.plist.disabled-D24` | descoped | not loaded | — | §2.12 |
| `security-audit.plist.disabled-D24` | descoped | not loaded | — | §2.12 |

**colima + colima-socket-watchdog (P6-57).** `ai.openclaw.colima` (`colima start`, RunAtLoad, **KeepAlive=false**) brings the container runtime up at login — this **SUPERSEDES** any "manually `colima start` after a reboot" guidance; a warm reboot autostarts it (cold-boot still halts at FileVault, §2.1). Because KeepAlive is false, a dead/wedged colima does **not** self-heal — that gap is closed by `colima-socket-watchdog` (every 5m): it probes the real docker control-plane (`docker info` over the Colima socket) and only on **two** failed probes (transient-blip reject) past a 15m cooldown restarts Colima and Telegrams Peter. Verify: `tail ~/.openclaw/logs/colima-socket-watchdog.log` → `ok docker socket reachable`. Run by hand: `bash ~/.openclaw/scripts/colima-socket-watchdog.sh` (idempotent). Recover by hand: `colima status; colima restart` (or `colima start` if stopped) — the gateway (KeepAlive) brings the sandboxed agents back with it. **Do not** flip colima's KeepAlive to true — it fights a deliberate stop and can't catch a wedged-but-alive socket (the reason the probe watchdog exists).

**heartbeat-watchdog (P6-47 / WS-8/9).** Every 15m. Ensures today's per-agent memory file exists (stops the 07:27 read ENOENT), detects a stalled beat loop and restarts the gateway to recover it (cooldown-guarded so it can't restart-loop), runs the **WS-8 consolidated alerting** (fallback episodes / hard blockers e.g. dead OAuth / cost-context bloat → escalated to Peter via Marie's working channel), and carries the **[P6-121] intra-day `:main` auto-reset net** (a single heavy browser turn can balloon `:main` past ~250K inputTokens → it force-runs `session-freshstart --force --agent <id>`). Liveness is anchored on the **actual** last beat (`heartbeat last`), not a proxy. **[P6-59] cautionary tale:** an earlier watchdog keyed off a proxy signal that only the watchdog itself moved, so a *broken* watchdog silently starved `pro`'s beat ~20h (a sawtooth dead-zone) — the standing rule is watch the real per-event signal. Honours the pause/night sentinels (§2.14). Verify: `tail ~/.openclaw/logs/heartbeat-watchdog.log`. Dry-run by hand: `WATCHDOG_DRYRUN=1 bash ~/.openclaw/scripts/heartbeat-watchdog.sh` (prints intended sends/restarts/resets; acts on nothing).

**heartbeat-nightwindow ([P6-85]).** Fires 23:00 (suppress) and 07:00 (restore). OpenClaw has no native quiet-hours knob and a beat costs even when silent, so this disables beats overnight — it drops a `logs/heartbeat.night` sentinel the watchdog treats like a pause (so the long gap isn't read as a stall) — and re-enables + stamps a resume grace-marker at 07:00. **Manual pause precedence:** if `logs/heartbeat.paused` exists (Peter's §2.14 bleed-stop) the job yields and does nothing — it must never auto-`enable` over a manual pause. Idempotent (acts on the current hour, robust to sleep/wake). Dry-run: `NIGHTWINDOW_DRYRUN=1 bash ~/.openclaw/scripts/heartbeat-nightwindow.sh`.

**session-freshstart ([P6-82]/[P6-85]).** 06:55 daily. Archives then clears **only** the `agent:<id>:main` session (store entry + transcript) for `pa` and `pro`, so each morning's background session reloads genuinely lean from the memory files instead of a growing compaction summary; human `:telegram:direct:*` chats are **never** touched; archive-first ⇒ fully recoverable. Guards: skips a `:main` touched within 10m (in-flight turn); quiet-window-gated unless `--force`. Also invoked `--force --agent <id>` by the watchdog's intra-day reset net. By hand: `bash ~/.openclaw/scripts/session-freshstart.sh --dry-run` (shows selection, mutates nothing) or `--force --agent pa` for one agent.

**chat-bloat-reset ([P6-119]/[P6-129]/[P6-132]).** Every 15m. session-freshstart deliberately never touches the human chat, but Marie's `:telegram:direct` session re-bloats to ~150K within hours and compaction then hits its hardcoded 60s aggregate timeout → she sticks / goes quiet on Peter's next message. This archives + clears **only** that chat session and restarts the gateway **only** when all three guards hold: `totalTokens >= CHAT_RESET_TOKENS` (env, default 130K), idle `>= CHAT_IDLE_MIN` (20m — never mid-turn), last-reset `>= CHAT_COOLDOWN_MIN` (120m — no restart-loop). `pa` only (`pro` doesn't bloat). Marie reloads state from her memory files; only the raw recent buffer is cleared (archived, recoverable) — never fires mid-conversation. By hand: `bash ~/.openclaw/scripts/chat-bloat-reset.sh --dry-run` (prints the verdict line, mutates nothing).

**memory-health ([P6-119]).** 23:55 daily + RunAtLoad. The **Memory-Architecture-v2 canary**: reads each agent's trajectory + `openclaw memory status` + the daily memory files and computes the v2 acceptance-gate metrics (per-turn token median/p95/max, byte-truncation rate, compaction frequency, cache read:write, daily-file duplicate headings, index freshness). Writes a daily one-liner to `state/memory-health/daily.log`; raises a Telegram alarm to Peter **only** on a threshold breach **and** only when `ALARM_TELEGRAM=1` (default **off** until thresholds are calibrated on real data). Host-only, no billed turns. By hand: `DRYRUN=1 python3 ~/.openclaw/scripts/memory-health.py` (prints, sends nothing); `DAY=YYYY-MM-DD` scopes to a day.

**cost-notify ([P6-105]/[P6-107]).** Every 3m. Tails each agent's trajectory, groups `model.completed` turns into tasks and, once a task settles, pushes Peter a Telegram note with **exact** token counts + a $ figure. **Console is authoritative:** with an Anthropic **Admin API key** present (env `ANTHROPIC_ADMIN_KEY`, or `agent-creds/anthropic-admin.key` 0600, or 1Password), the message's daily total is the **actual** billed USD from the Cost Report API; the per-task $ is a small static estimate labelled `(est)`. Never asserts a local estimate as truth (matches the CLAUDE.md cost-discipline rule — defer to the Console for figures). Host-only, no billed turns. By hand: `DRYRUN=1 python3 ~/.openclaw/scripts/cost-notify.py` (prints); `BACKFILL=<hours>` (dry-run) replays recent history.

**reply-watcher (⑧, [P6-103]/[P6-104]; also the [P6-138] outbound-bounce interim sweep).** Every 15m. Polls a **bounded** Gmail query on the agent's own live direct-API cred (via `gmail_helper.py` **by PATH** — the script never reads the cred value) and, per new match, takes one **mode-gated** action: `REPLYWATCH_MODE=dryrun` (**DEFAULT** — poll+log only, zero spend, but the live poll still runs so scope is verifiable free), `notify` (free Telegram heads-up via the gateway bot send — not a billed turn), or `poke` (**BILLED** — wakes Marie via `openclaw agent --deliver`; hard-capped at `REPLYWATCH_MAX_PER_DAY`/day, downgrades to `notify` over the cap). Dedup via `state/reply-watcher.seen`. **Ships in `dryrun`** — do not flip to `notify`/`poke` without intent (`poke` spends). Verify: `tail ~/.openclaw/logs/reply-watcher.log`. It touches `agent-creds/**` (deny-Read) only through the helper — never surface those bytes.

**cred-save-watch — the cred-save subsystem ([P6-108]/[P6-114]/[P6-115]).** Every 45s; runs `cred-save-drain.sh`. The agents' sandboxes have **no `op` binary** (host-only, deliberate), so Marie/Charlotte can only **request** a credential save by dropping a JSON into their gitignored `workspace-<id>/.cred-queue/pending/`. The drain scans both queues, actions each via `cred-save.sh` → `op-put.sh`, and writes a **display-immune** receipt (item id + sha256-12/len) the agent reads back. Two modes: `generate` (op generates the password host-side for account provisioning — the request is non-secret; any secret key is refused), and `existing` (take-over of Peter's **non-sensitive** accounts — carries a `password` which is **redact-shredded** from the request after storage; `sensitive:true` / card / token / pin / otp requests are refused). On a successful store it also host-deletes Peter's credential message from Telegram if the request carried the chat/message id (agents can't — it's a host CLI). Verify: `tail ~/.openclaw/logs/cred-save-watch.out.log`; queue state under `workspace-<id>/.cred-queue/{pending,results,processed}`. By hand: `bash ~/.openclaw/scripts/cred-save-drain.sh --dry-run`.

### 2.16 Host secret-handling CLIs — autofill, payments, op-CLI wrappers

These are **not** scheduled jobs — they are host-only, point-of-use tools invoked by the operator/hook while an agent is **paused**. All keep the secret **off** the model's context and **off** argv (the value goes into a `0600` `--fields-file` created under `umask 077` and unlinked in a trap; every one has a `--dry-run` that verifies resolution display-immune — sha256-12 + length — with no fill and no navigation). `op` lives on the **host only**, never in a sandbox, so a compromised container never holds the vault token. Credential HYGIENE (§1, §1.5/§1.6) applies unchanged.

**op-get / op-put / op-list — the 1Password automation wrappers ([F70] resolution).** Driven by the OpenClaw **service-account** token (`OP_SERVICE_ACCOUNT_TOKEN`) — the unattended-safe auth path (no interactive `op signin`, no `OP_SESSION`), which is exactly what launchd needs. Token source order: env, else the `0600` deny-Read `agent-creds/op-service-account.token` (Peter provisions it; the scripts never handle the value). `op-get.sh 'op://OpenClaw/<item>/<field>'` emits a value for piping (or `--verify` → sha256-12 + len only), and **refuses any non-`op://OpenClaw/*` ref** (least privilege — single-vault scope). `op-put.sh` creates + stores **agent-provisioned** accounts into the dedicated **`Agent-Provisioned`** vault (never the OpenClaw ops vault where SA/tokens/the Marie PA card live), with an op-generated password and a consistent schema/tags. `op-list.sh` prints **only** non-secret structure (vault names, item titles/categories, field ids/labels — never a value). Keep the service account **read-only-or-scoped** in the 1Password console — verify least privilege there, not by assumption.

**cred-fill — account self-provisioning last-mile ([P6-136], built + proven E2E).** Fills an op-**generated** password into a live signup form so an agent creates an account **without ever seeing the password** ("gate at value"). Ordering: `cred-save.sh --mode generate` must have already generated+stored it (the receipt gives `op://Agent-Provisioned/<id>/password`); cred-fill reads **that** ref and fills the snapshot refs the paused agent surfaced (it never generates and never reads back into the sandbox). Refuses any non-`Agent-Provisioned/*` ref (cards / ops secrets out of scope). Dry-run: `cred-fill.sh --op-ref op://Agent-Provisioned/<id>/password --password-ref R1 --dry-run`.

**pay-fill — autonomous payments (6c.19).** Host fills card fields at a checkout **by 1Password ref** (Marie snapshots the payment page and surfaces field refs — she sees refs, never values; the card is resolved via op-get by UUID from the OpenClaw vault; `--card uk|intl` selects Revolut/N26). Real payments are **irreversible** → strict **prepare-and-surface**: the host fills only **after** Marie surfaced "ready to pay" and Peter approved, with no agent browser-read turn between fill and submit. **KNOWN LATENT PATH BUG — flagged for the Andreas payment work:** `pay-fill.sh` hardcodes `PATH=/opt/homebrew/bin:/usr/bin:/bin:/usr/sbin:/sbin`, **missing `~/.npm-global/bin`**, so an `openclaw` call under a minimal launchd/hook env can fail to resolve the CLI. This is the **same class** as the cred-fill PATH bug **fixed live at 6c.30** (cred-fill now carries `$HOME/.npm-global/bin` first). **Fix pay-fill's PATH before wiring it into any unattended caller.** Dry-run: `pay-fill.sh --ccnum-ref R1 --expiry-ref R2 --cvv-ref R3 --dry-run`.

## 3. Quick-reference card

### 3.1 Key paths

```
~/.openclaw/                            — root of all operational state
  openclaw.json                         — main config (10489 B / sha12 3267edd8b6aa, mode 0600, A-7 clean; pin 2026.4.22)
  secrets.json                          — API keys plaintext (~245 B, mode 0600)
  credentials/                          — per-credential files (all mode 0600)
  agents/{id}/sessions/sessions.json    — per-agent session store index
  memory/{id}.sqlite                    — per-agent local memory DB
  workspace-{id}/                       — per-agent authored templates (currently absent for all)
  shared/                               — inter-agent shared folders (master.md + daily-digests/ + todos/)
  logs/gateway.log                      — lifecycle log
  logs/gateway.err.log                  — security/audit log

~/Library/LaunchAgents/   (11 live + 2 descoped; all gui/502 — bootstrap/bootout GUI-only per D20; full ops table §2.15)
  ai.openclaw.gateway.plist                          — gateway supervisor (KeepAlive+RunAtLoad, 2309 B, §2.1)
  ai.openclaw.colima.plist                           — Colima runtime start at login (RunAtLoad, KeepAlive=false, §2.15)
  ai.openclaw.colima-socket-watchdog.plist           — docker-socket health probe, every 5m (§2.15)
  ai.openclaw.heartbeat-watchdog.plist               — beat-stall + WS-8 alerting + :main reset net, 15m (§2.14/§2.15)
  ai.openclaw.heartbeat-nightwindow.plist            — suppress/restore beats 23:00/07:00 (§2.15)
  ai.openclaw.session-freshstart.plist               — daily lean :main reset, 06:55 (§2.15)
  ai.openclaw.chat-bloat-reset.plist                 — Marie chat self-heal when bloated+idle, 15m (§2.15)
  ai.openclaw.memory-health.plist                    — memory-v2 canary, 23:55 + RunAtLoad (§2.15)
  ai.openclaw.cost-notify.plist                      — per-task token+$ push, 3m (§2.15)
  ai.openclaw.reply-watcher.plist                    — bounded Gmail poll (dryrun default), 15m (§2.15)
  ai.openclaw.cred-save-watch.plist                  — credential-save queue drain, 45s (§2.15)
  ai.openclaw.vault-backup.plist                     — Obsidian→gdrive daily 04:00 (1122 B, §2.9)
  ai.openclaw.calendar-backup.plist.disabled-D24     — descoped (D24); not loaded (§2.12; verify at 6d)
  ai.openclaw.security-audit.plist.disabled-D24      — descoped (D24, F68); not loaded (§2.12)
```

### 3.2 Key commands

```bash
# Health
curl http://127.0.0.1:18789/healthz                                    # is gateway alive?
openclaw doctor --non-interactive --no-workspace-suggestions           # full read-only sweep
openclaw models status                                                 # inference provider state

# Inspection
openclaw memory status                                                 # per-agent memory health
openclaw sessions --agent <id>                                         # per-agent session list
openclaw devices list                                                  # SSH-side device pairings
launchctl list | grep ai.openclaw                                      # LaunchAgent fleet (11 live; §2.15)
launchctl print gui/$(id -u)/ai.openclaw.<job> 2>&1 | head -3          # per-job state — F69: head-3, NEVER head-1

# 1Password
op whoami                                                              # session active?
eval $(op signin)                                                      # re-authenticate
op read "op://OpenClaw/<note>/<field>" --no-newline > /dev/null && echo ok

# Maintenance (state-changing — pause first)
openclaw doctor --fix                                                  # cosmetic repairs only
openclaw memory index --force                                          # rebuild index for one agent
# bootout/bootstrap are GUI-session-only (D20): over SSH they exit 125 — use Screen Sharing.
launchctl bootout gui/$(id -u) ~/Library/LaunchAgents/ai.openclaw.gateway.plist
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/ai.openclaw.gateway.plist
```

### 3.3 Key network values

- Gateway: `127.0.0.1:18789` (loopback only)
- Mini on Tailscale: `100.84.220.122` (admin SSH path; tailnet only)
- 1Password shard: `https://my.1password.eu`
- All cloud APIs: outbound only over HTTPS

### 3.4 "When in doubt" recipes

- **System feels slow but no errors:** `openclaw doctor --non-interactive --no-workspace-suggestions` and read the output. Most issues surface there.
- **Gateway healthz times out:** Don't restart immediately. Read `gateway.err.log` first (§2.1 step 1). If err log shows repeated crashes, follow §2.1. If err log is quiet, give it 30s and retry — could be transient load.
- **Cannot run an op command:** First try `op whoami`. If "not signed in", run `eval $(op signin)`. If signin fails, escalate — 1Password is recovery-critical.
- **Sha12 of a config file differs from the anchor in DEVIATIONS-LOG.md:** Don't assume corruption — could be a legitimate edit. Compare contents (§2.7) and establish whether the change is intentional before any recovery action.
- **Anything touching credentials:** Read §1 first. Then §2.2. Then ask Peter. Credentials are not a "fix it and move on" surface.

## 4. Glossary

*(Aligned with STATE-OF-NATION §5; a few Phase-0 operational terms added so this document is self-sufficient in an incident.)*

- **SecretRef.** A reference in `openclaw.json` pointing at a secret rather than embedding the secret directly. Resolves to a value via `secrets.json`, auth-profiles, or filesystem credential, depending on the surface.
- **Auth-profiles.** A resolution surface for some credentials (notably Anthropic). Materialise-once: rotations require explicit re-materialise (per F25), gateway restart alone won't refresh.
- **F-row, D-row, S-row, M-row.** Tracking categories in `DEVIATIONS-LOG.md`. F-row = deviation (a way the live system differs from the spec or expected behaviour). D-row = decision (a committed choice with rationale). S-row = sub-fact (observation logged for future reference). M-row = maintenance item (recurring or scheduled hygiene task).
- **`allowFrom` file.** Per-channel whitelist of allowed inbound sender IDs. Currently only PA has one (`telegram-pa-allowFrom.json`).
- **Heartbeat.** Scheduled per-agent awareness check. Lightweight (~2–3K context, frequent) or full (~8–12K context, less frequent). See STATE-OF-NATION §3.4.
- **Autonomy level (L0–L5).** Per-agent operational latitude. L0 = read-only, L5 = proactive. Each agent has a per-week progression path per architecture §3.3.
- **gog.** The Google API wrapper (Gmail + Calendar). Stores tokens in a keyring under `~/.openclaw/credentials/`, unlocked by `GOG_KEYRING_PASSWORD`.
- **ClawHub.** The community skill registry. Read-only via `openclaw skills search --json`. All installs go through a manual D2 source-review gate (per F57 — the CLI manifest is too thin to substitute for review).
- **F14 v2 chat-layer linkification.** The chat interface auto-converts any dotted-TLD string (`.json`, `.md`, `.sh`) into a clickable link, which can obscure the actual bytes. Defense pattern: use sha12 + byte-length as verification proxies; use shell variables for paths in commands.
- **F17 staging pattern.** Always stage credential file edits as `<filename>.new` in the same directory as the target, then rename-only `mv`. Never `mv` from `/tmp` (macOS regresses permissions on tmp-rooted moves).
- **F25 re-materialise.** Auth-profile-resolved credentials require explicit re-materialise after rotation; restart alone won't refresh them.
- **F44.** The calendar-backup-token's 7-day TTL issue. Currently moot — calendar-backup is descoped (D24, §2.12); F44 resurfaces only on re-enable (Phase G, ~2026-07-23± — date/status unverified at 6c.31, confirm at 6d), which includes the OAuth app Internal/Production flip that removes the 7-day TTL.
- **F45 ceremony.** The gateway restoration procedure for when automatic `KeepAlive` recovery has not kicked in. See §2.1.
- **Daily digest.** Per-agent structured summary written at nightly consolidation. Stored in `~/.openclaw/shared/daily-digests/YYYY-MM-DD/{agent}.md`. CoS reads all; other agents read relevant ones.
- **Re-baseline.** Capturing fresh sha12 + byte-size anchors for a runtime-mutable file (notably `models.json`) at session boundaries to detect inter-session drift.
- **vault-backup.** The 04:00 daily LaunchAgent that `rclone sync`s the Obsidian vault to Google Drive (+ dated archive). See §2.9.
- **op-backup.** Interactive script producing an encrypted 1Password vault snapshot (`onepassword-vault-latest.enc`). Unattended version deferred (F70). See §2.10.
- **Config repo (F72).** Private GitHub repo `pfedchen-openclaw/openclaw-config` holding the *sanitised* config + scripts + launchagents as a DR layer — placeholders, never live secrets. See §2.11.
- **Sanitised snapshot.** `openclaw.sanitised.json` — `openclaw.json` with inline Telegram tokens redacted to placeholders. A snapshot: refresh via `sanitise_openclaw.py --write` after any `openclaw.json` change (§2.11).
- **`.disabled-D24` suffix.** Durable descope: a parked plist renamed off the `*.plist` pattern so a GUI login can't silently re-load it (F67). Re-enable per §2.12.
- **rclone.** Sync engine behind vault-backup (`/opt/homebrew/bin/rclone`, v1.74.2) with a scope-confined gdrive remote.
- **F61.** The embedding credential resolves via the per-agent `openai:default` auth-profile, not `secrets.json`; new agents need it provisioned before their first embedding call.
- **F68.** launchd's minimal PATH omits `/opt/homebrew/bin`; any launchd job using a Homebrew binary needs an `EnvironmentVariables` PATH or it fails silently (was the security-audit failure).
- **F69.** Tahoe `launchctl print` emits `Bad request.` as a cryptic line-1; always `head -3` (never `head -1`) to read the real diagnostic.
- **P6-N / PHASE-6-DEVIATIONS-LOG.md.** The **live** deviations log for Phase 6 (P6-N codes, plus new F/D/S findings). Additive-only. The Phase-0 `DEVIATIONS-LOG.md` is frozen. See §0.
- **colima / colima-socket-watchdog.** `colima` autostarts the container runtime at login (RunAtLoad, KeepAlive=false — supersedes any manual-start-after-reboot step); the watchdog (5m) probes the docker socket and restarts Colima on a wedged-but-alive socket. See §2.15.
- **heartbeat-watchdog.** 15m beat-stall supervisor: restarts the gateway on a stalled loop, runs WS-8 alerting, carries the intra-day `:main` reset net. [P6-59] taught it to watch the *real* beat signal, not a proxy (a broken watchdog once starved `pro` ~20h). See §2.14/§2.15.
- **heartbeat-nightwindow.** Suppresses beats 23:00–07:00 (cost) via a `heartbeat.night` sentinel; manual pause takes precedence. See §2.15.
- **session-freshstart.** 06:55 daily archive+clear of the `:main` session so it reloads lean; human chats untouched; archive-first. See §2.15.
- **chat-bloat-reset.** Self-heals Marie's `:telegram:direct` chat when both bloated (`CHAT_RESET_TOKENS`) and idle, then restarts the gateway; archive-first, `pa` only ([P6-119]/[P6-132]). See §2.15.
- **memory-health.** Daily Memory-Architecture-v2 canary; logs metrics, Telegram-alarms only on a breach with `ALARM_TELEGRAM=1`. Host-only. See §2.15.
- **cost-notify.** 3m per-task token+$ push; daily total from the Anthropic Cost Report API when an Admin key is present — Console is authoritative, never a silent local estimate. See §2.15.
- **reply-watcher.** 15m bounded Gmail poll surfacing awaited replies; modes `dryrun` (default, free) / `notify` (free) / `poke` (billed, day-capped). [P6-138] outbound-bounce interim sweep. See §2.15.
- **cred-save subsystem.** Host-mediated credential SAVE for the sandboxed agents (no `op` in-sandbox): they queue a request in `workspace-<id>/.cred-queue/`, `cred-save-watch` (45s) drains it via `cred-save.sh`→`op-put.sh`; `generate` vs `existing` (shredded) modes. See §2.15.
- **cred-fill / pay-fill.** Host point-of-use autofill while the agent is paused: cred-fill injects an op-generated **password** into a signup form ([P6-136]); pay-fill injects **card** fields at a checkout by 1Password ref (prepare-and-surface; irreversible). Secret never enters the model context. pay-fill has a latent PATH bug (missing `~/.npm-global/bin`) flagged for the Andreas payment work. See §2.16.
- **op-get / op-put / op-list.** Host-only 1Password wrappers over the read/write **service account** (`OP_SERVICE_ACCOUNT_TOKEN`), least-privilege vault-scoped (get refuses non-`OpenClaw`; put targets the dedicated `Agent-Provisioned` vault; list prints structure only). Closed [F70]. See §2.16.

---

*System architecture, agent roster, and dependency map are in the companion PROJECT-STATE-OF-NATION.md.*

**End of document.**
