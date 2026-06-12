# SESSION 6c.5 — Local documents (docx/xlsx edit → PDF → email attachment) — CLOSURE
_2026-06-12. Wired the end-to-end local-document workflow for **Marie & Charlotte** and added **email-attachment support** on the draft path. Resolves **P6-36** (bootstrap-cap truncation); records **P6-38** (the docs + attachment capability). Stages **P6-39** (browser trust-model decision) in §7, to be applied at 6c.6 Stage 0._

## §1 Outcome
Both live agents can now **edit Word/Excel in-sandbox and export PDF**, and **Marie can draft an email with a produced PDF attached — surfaced for approval, never sent**. The credential-touching attachment lives in the RO-bound canonical `gmail_helper.py` (Peter's option-A choice); the no-creds docx/xlsx→PDF lives in a workspace `docs_helper.py` delivered like the browser helper. The **no-send floor is preserved** (drafts only; verified DRAFT-labelled in the live mailbox). Scratch (LibreOffice profile + conversion temp) is forced to `/tmp` — `/workspace` stays clean (discharges the P6-31/P6-34 HOME/scratch carry for documents). The **bootstrap-cap truncation is fixed** (`bootstrapTotalMaxChars` 60000→75000, activated by a gateway restart; TOOLS.md now injects whole). Proven end-to-end through **real agent turns** (F26) for both agents. **6c.5 CLOSED.**

## §2 What was done (arc)
- **Stage 0 (verify-only).** Deviations log current (P6-34 DONE / P6-35+P6-36 OPEN, latest P6-37). Gateway pid **91510** / healthz 200; **A-7 live==last-good `a7701f51d6d1`** (8926B); both containers `capable-2026-06 ✓`; pa/pro `claude-opus-4-7` + `thinkingDefault high`; helper shas (browser `0e2f72aa4c1b`, gmail `5fd20fd379a7`, google `15566ad20307`) intact; office toolchain green. **Session ran over SSH** → D20 binds (gateway restart blocked) and the F21/F51 bash gate blocks `openclaw.json` + `agent-creds/**`.
- **P6-36 cap raise (first action, Peter-approved).** F17 surgical staged edit (single value 60000→75000, byte-delta 0) → atomic mv (live `81641e9994ca`). A clean turn confirmed it **does not hot-reload**. Peter restarted the gateway (Screen Sharing); post-restart `systemPromptReport` → cap=75000, **zero truncation, TOOLS.md `injected==raw`**. A-7 re-anchored.
- **Recon + build (gated, reversible).** Read `gmail_helper.py` (display-immune secret scan first — pure code, no secret values) and extended its `draft` verb with `--attach`/`--dry-run`. Authored `docs_helper.py` (docx/xlsx edit + `to-pdf`, HOME=/tmp baked in). **Smoke-tested both in a faithful `--read-only`-root throwaway** (PDF produced, scratch in /tmp, attachment MIME `multipart/mixed` validated offline, missing-file refused), and a live-container pre-flight.
- **Deploy.** `docs_helper.py` → both workspaces (rw bind, instant). `gmail_helper.py` staged canonical at `agent-tools/`; because the bind source `agent-creds/gmail_helper.py` is bash-gated for this session, **Peter** ran (Screen Sharing): `cp` into `agent-creds/` → `sandbox recreate --agent pa`/`--agent pro` → `gateway restart`. Containers re-resolved the RO bind to the new helper.
- **Docs (P6-24).** Both TOOLS.md (docs section + `--attach`) + AGENTS.md (self-sufficient documents line); Charlotte's attachment-draft gated dormant per role.
- **F26 (real agent turns).** See §4.

## §3 End-state (anchors)
- **openclaw.json:** **`81641e9994ca`** (8926B), cap **75000**; **A-7 CLEAN** (live==last-good, `cmp` byte-identical). Gateway pid **83694**, healthz 200. *(Live sha known from the staged-apply; bash reads of `openclaw.json` are gate-blocked this session.)*
- **`docs_helper.py`:** **`52cabe7778bb`** (6029B) — `agent-tools/` (canonical) + `workspace-pa/` + `workspace-pro/` + `/workspace/docs_helper.py` in both containers.
- **`gmail_helper.py`:** **`d9858a84b60d`** (7599B) — `agent-tools/` (canonical) + `agent-creds/` (via Peter) + `/opt/gmail_helper.py` in both containers; `--attach`/`--dry-run` live. (Was `5fd20fd379a7`.)
- **Marie (`pa`):** TOOLS **`3bcd770661f8`** (9653B), AGENTS **`a3773df307ed`** (8008B).
- **Charlotte (`pro`):** TOOLS **`74cfea28782c`** (9588B), AGENTS **`416fd6ea9c26`** (8826B).
- **Unchanged:** `browser_helper.py` `0e2f72aa4c1b`, `google_helper.py` `15566ad20307`; image `openclaw-sandbox:capable-2026-06`; pa/pro `claude-opus-4-7` thinking `high`; binds = gmail_helper + `<mailbox>-gmail.json:/creds/google.json` + google_helper (ro) + auto `/workspace` (rw).

## §4 F26 proof (real agent turns, `claude-opus-4-7`, 0 tool failures)
- **Marie (`--agent pa`):** created `/workspace/memo_6c5.docx`, appended a 2nd paragraph (`docx-info` → 3 paras), exported `memo_6c5.pdf` (`%PDF-`, 14448B), then drafted an email to herself with it attached → **draftId `r-3926511610880964218`, messageId `19ebdb3e1db03b6e`**. **Re-fetched the message to verify independently:** labels `['DRAFT']`, subject "6c.5 docs+attachment test", To pfedchen@gmail.com, parts `multipart/mixed → text/plain(82B) + application/pdf memo_6c5.pdf(14448B)`, `sent:false`. The docx→edit→PDF half ran on a turn earlier (cap still 60000) and again post-restart — both green.
- **Charlotte (`--agent pro`):** produced `cstest_6c5.pdf` (valid) via docs_helper; a separate real turn read `example.com` and had the **egress guard refuse the host gateway `192.168.5.2:18789` (exit 3)**.
- **Regressions green both agents:** Gmail list (pfedchen + soveren mailboxes), `google_helper` calendar-list, browser read. **Scratch clean** — only intended deliverables landed in each `/workspace`; no new LO scratch.

## §5 Decisions taken / surfaced this session
- **Attachment path = option A** (extend RO-bound `gmail_helper.py`; `sandbox recreate`) — Peter, 6c.5. Rationale + the cred-confidentiality nuance: see **P6-38**.
- **Cap activation = gateway restart now** (Screen Sharing) rather than deferring — Peter, 6c.5. Confirmed the cap does not hot-reload (P6-36 resolution).
- **Browser reliability (P6-37) → directed to option (b)** as the 6c.6 primary path — see §7 / **P6-39**.

## §6 Outstanding / carried
- **Workspace clutter (P6-36 carry):** stale 6c.2-era `.cache`/`.config` in both workspaces — one-time delete, surfaced not auto-removed.
- **Signature-PNG insertion — still deferred** (P6-33); build later against real examples.
- **Docs/Sheets *writes* via API still blocked** (P6-32) — produce documents via LibreOffice/python-docx locally (as 6c.5 does), not the Google Docs API.
- **P6-35 host-egress hardening → 6c.7** (unchanged). **SR-2** ICS-before-calendar-write — still open. **Correspondence pattern-review** before 6d. **Browser-helper `/opt` promotion** carry (next GUI session).
- **F28 cold-reboot + F45 + scheduling reliability (P6-29) + chat-surface hygiene (P6-27/P6-30) + cost/backup → 6c.7.** **PENDING-BATCH-APPLY (P6-11/12/14) + Charlotte avatar (P6-20) → 6c.7.**

## §7 Ledger — rows to apply at 6c.6 Stage 0 (additive-only, original byte-recoverable, SHA-256 verified)
_Already applied to `PHASE-6-DEVIATIONS-LOG.md` this session: **P6-36 RESOLVED** (append) + **P6-38** (new). The row below is staged here and is to be **lifted into the log at 6c.6 Stage 0** as **P6-39**._

**P6-39 — Browser trust model & full-site access (decision; execute at 6c.6).**
(a) **Tiered trust:** outbound-trusted (agent/Peter-initiated to reputable destinations) → host real browser, relaxed isolation; inbound-untrusted (third-party links) → in-container headless, untrusted content, strict gates.
(b) **In-house host real browser** (real Chrome + warmed profile + residential Mini IP + stealth, behind a minimal-privilege CDP bridge with SSRF guard) is **primary**; clears the fingerprint/TLS/IP/JS-challenge tier (BA/Akamai/Cloudflare-JS).
(c) A **third-party CAPTCHA-solving service** is a residual fallback so Peter is **never asked to solve CAPTCHAs** (sees only the challenge); **managed-remote browser deferred** as last resort for hard-refuse sites.
(d) **Consequential-action gates unchanged:** payments/account-creation/PII = **L1 prepare-and-surface**, Peter confirms.
**Rationale:** the sandbox guarded low outbound-injection risk; the real safety net for booking is the payment gate; host-browser-behind-a-bridge applies the existing messengers/voice host-bridge pattern. **Honest limit:** realism + solver clears the vast majority; a rare hard-refuse site may still need the deferred managed-remote tier.

---
_Attachments for 6c.6: this closure + PHASE-6-DEVIATIONS-LOG.md (P6-36 resolved / P6-38 / P6-37 directed) + CAPABILITIES-ROADMAP-TO-6D (row 6c.6) + the 6c.6 opener. Older closures on demand._
