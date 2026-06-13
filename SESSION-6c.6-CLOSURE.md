# SESSION 6c.6 — Trusted-path host browser — CLOSURE
_2026-06-13. Gave Marie & Charlotte a **host real-Chrome browser** that clears the bot-walls in-container headless couldn't (P6-37), via OpenClaw's **native** browser subsystem (Peter greenlit native-over-hand-rolled + Google Chrome at Stage 1). Resolves **P6-39** (DONE); records **P6-40** (playwright packaging-gap host fix). **6c.6 CLOSED** on the core; deeper per-site pre-payment agent flows carried to 6c.7._

## §1 Outcome
Both live agents now have a **two-tier browser**: **Tier 1** = real headful Google Chrome on the Mini (residential BT IP, warmed persistent profile) driven by the **gateway** over loopback CDP and exposed to the sandboxed agents as the native **`browser` tool**; **Tier 2** = the existing in-container headless `browser_helper.py` (6c.4) for untrusted/off-allowlist links. Trust routing is **code-enforced** by a `browser.ssrfPolicy.hostnameAllowlist` (the host browser physically cannot navigate off the approved list). Stealth passes standard bot detection with **no custom layer**. The container never touches CDP/host:18800 (gateway-mediated) → **P6-35 not reopened**. Consequential-action gates unchanged (payments/account/PII = L1 prepare-and-surface). **F26: Marie drove Tier 1 to British Airways → real homepage, no Akamai wall**; all four exit sites (BA, Trainline, Aman, Four Seasons) load real content with zero challenge flags. The opener's hand-rolled CDP bridge (Stages 3–4) was **superseded by native plumbing**; CAPTCHA solver (Stage 5) **not needed** for any exit site (not built, per A-12).

## §2 What was done (arc)
- **Stage 0.** Lifted **P6-39** into the log (additive; log `76fd23ec3cb3`→`10fc45d203b1`). Read-only sanity **ALL-GREEN** (gateway pid 83694/healthz 200; pa+pro `claude-opus-4-7`; both containers `capable-2026-06`; **A-7 clean** `81641e9994ca`/8926B; op SERVICE_ACCOUNT; no `ANTHROPIC_API_KEY` → subscription). Session over **SSH**.
- **Stage 1 recon → greenlight.** Found OpenClaw 2026.4.22 ships native browser support (plugin loaded, `openclaw browser` CLI, gateway browser-control service, full `browser.*` config + native `ssrfPolicy`). No host browser installed; egress IP residential (AS2856 BT). **Surfaced two decisions; Peter chose Native subsystem + Google Chrome.**
- **Stage 2 (host browser).** Installed genuine **Chrome 149** → `~/Applications` (no admin), auto-detected. `openclaw browser start` spawns headful Chrome over SSH (gateway=gui/502). Hit **P6-40** (playwright-core not resolvable from `dist/` level) → symlinked the bundled copy to pkg-root `node_modules` + restart → `navigate`/`snapshot` work.
- **Stage 3/4 (native bridge + routing + stealth).** No bridge to build — native service IS it. Set `browser.ssrfPolicy.hostnameAllowlist` (wildcards + loopback) → enforced trusted-destination routing (off-list refused). Stealth probe (`bot.sannysoft.com`) clean. All config via `openclaw config set` (gate-clear, schema-validated, atomic).
- **Stage 5 (CAPTCHA).** Not needed — no exit site presented a challenge. Documented as contingency.
- **Stage 6 (wire pa+pro, P6-24).** Three layers to expose the host browser to a sandboxed agent: base `tools.alsoAllow`, sandbox `tools.sandbox.tools.alsoAllow`, and `sandbox.browser.allowHostControl=true` — then gateway restart **+ `sandbox recreate --force`** (mandatory). Per-agent for pa+pro only. TOOLS.md/AGENTS.md two-tier docs updated both agents.
- **Stage 7 (exit).** F26 real turns (Marie BA, Charlotte example.com); all 4 sites load real content. Deeper per-site pre-payment flows carried.

## §3 End-state (anchors)
- **openclaw.json:** **`c48574d5ab10`** (9928B), perms `-rw------- staff`; **A-7 CLEAN** (`.last-good` byte-identical). Vendor `openclaw.json.bak` = `4ede3435b908` (rollback chain). Gateway healthz 200 (restarted ~5× over SSH this session).
- **Chrome:** `~/Applications/Google Chrome.app` v149.0.7827.115 (Google LLC, codesigned). Profile `~/.openclaw/browser/openclaw/user-data`. CDP `127.0.0.1:18800`, headful.
- **playwright-core fix:** symlink `…/openclaw/node_modules/playwright-core` → `…/dist/extensions/browser/node_modules/playwright-core` (P6-40 — **wiped by `openclaw update`; must re-apply**).
- **Config added:** `browser.ssrfPolicy.hostnameAllowlist` [127.0.0.1, localhost, example/BA/ba/Trainline/thetrainline/Aman/FourSeasons (+`*.`), bot.sannysoft.com, browserleaks]; pa+pro each: `tools.alsoAllow`+=browser, `tools.sandbox.tools.alsoAllow`+=browser, `sandbox.browser.allowHostControl=true`.
- **Docs:** pa TOOLS `1cc4fb623987` / AGENTS `88d55a026d19`; pro TOOLS `2632d397b270` / AGENTS `41250dc2e6d3`.
- **Deviations log:** **`8847796b236e`** (90255B).
- **Unchanged:** image `capable-2026-06`; pa/pro `claude-opus-4-7` thinking high; helpers browser/gmail/google/docs; 6c.5 binds.

## §4 F26 proof (real agent turns, `claude-opus-4-7`)
- **Marie (`--agent pa`):** Tier-1 `browser` tool → `navigate`+`snapshot` BA homepage → REAL site (Discover/Book/Manage/Help/Search/Log in), **no bot-wall**; named Tier-1 used + Tier-2 fallback. (Two earlier attempts correctly self-reported the tool missing — P6-22 discipline held — until the 3-layer exposure + recreate landed.)
- **Charlotte (`--agent pro`):** `browser` tool in palette → navigated example.com ("Example Domain"); confirmed untrusted→`browser_helper.py` routing.
- **CLI host-browser regression:** BA + Trainline + Aman + Four Seasons all load real content, **zero challenge flags**; non-allowlisted google/duckduckgo correctly **refused**; stealth probe clean. Tier-2 `browser_helper.py` untouched (still routes untrusted).

## §5 Decisions taken
- **Native subsystem over hand-rolled CDP bridge** (Peter, 6c.6 Stage 1) — less code, gateway-mediated, vendor-maintained, doesn't reopen P6-35. **Google Chrome** as the binary (Peter).
- **Enforced allowlist trust routing** (refinement, this session): the SSRF `hostnameAllowlist` makes trusted-destination routing a code control, not just instruction — recorded in P6-39. Reads as MORE restrictive than the opener; reversible/expandable via `openclaw config set`.
- **CAPTCHA solver deferred to contingency** (empirical, A-12) — no exit site needed it.

## §6 Outstanding / carried (→ 6c.7)
- **Deeper per-site pre-payment agent flows** (Trainline Verona→Milano dates; Aman/Four Seasons availability for given dates; BA fare search) as real F26 turns — the remaining exit breadth. Sites are proven loadable; the multi-step search→pre-payment via agent turns is the next exercise.
- **Warmed-profile logins** (Screen-Sharing) for any login-walled flow / loyalty/saved-traveller; not needed for public search/guest checkout.
- **P6-40 symlink is `openclaw update`-fragile** — add re-apply to runbook / one-touch script.
- **Allowlist curation** — extend `hostnameAllowlist` as new reputable sites arise (esp. professional domains for Charlotte; current list is travel-focused).
- **Standing:** SR-2 (ICS before calendar write); correspondence pattern-review before 6d; managed-remote browser deferred; workspace `.cache`/`.config` cruft (P6-36 carry); signature-PNG (P6-33); Docs/Sheets API writes blocked (P6-32); P6-35 host-egress hardening; F28/F45/scheduling/chat-hygiene/cost/backup; PENDING-BATCH-APPLY (P6-11/12/14) + Charlotte avatar (P6-20).

## §7 Ledger — rows to apply at 6c.7 Stage 0
_Already applied to `PHASE-6-DEVIATIONS-LOG.md` this session: **P6-39 RESOLVED** (append) + **P6-40** (new). No staged rows pending lift; the carries above feed the 6c.7 opener directly._

---
_Attachments for 6c.7: this closure + PHASE-6-DEVIATIONS-LOG.md (`8847796b236e`) + CAPABILITIES-ROADMAP-TO-6D + the 6c.7 opener._
