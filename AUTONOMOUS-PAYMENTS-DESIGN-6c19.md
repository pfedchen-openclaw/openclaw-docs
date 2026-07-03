# Autonomous capped-card payments — architecture & trust boundary (6c.19)
_Design artifact. Peter's directive (6c.19): "build the isolated capability properly. I'm not interested in a superficial $5 test." Supersedes the quick-proof path. Author: Claude Code, 2026-07-03. Status: **DESIGN — awaiting Peter's trust-boundary sign-off before the live-brief edits + login establishment.**_

## 1. Goal (Peter's stated model)
The agent (Marie) drives the **whole** flow on **any** website: logs itself in (existing credential / request from Peter / create an account), goes through checkout, reaches payment. **Peter's only touch-point is approving the charge + the 2FA.** No per-login involvement. Isolated, persistent agent browser — never his personal Chrome. (See memory `agent-payments-autonomy-model`.)

## 2. Key finding — the primitives already exist
Recon (6c.19) confirmed this is **wiring existing primitives + graduating the brief**, not a novel bridge:
- **Shared host browser.** The agent-callable `browser` tool drives the **host-side managed `openclaw` Chrome profile** (persisted at `~/.openclaw/browser/openclaw/`), and the `openclaw browser` **CLI acts on the same tab**. Host and agent share one session.
- **Sandbox → host control is ON.** `agents.list[pa].sandbox.browser.allowHostControl = true` (also `pro`). Sandboxed Marie legitimately drives the host profile. No config change needed.
- **Sessions persist + are reused.** Docs (`tools/browser-login`): operator/host logs a site in once → the login persists in the `openclaw` profile → the agent reuses it. Docs also warn *"do not give the model your credentials"* (anti-bot + trust) → **the host fills credentials, never the model.**
- **Card retrieval works, host-only.** `scripts/op-get.sh` (service-account `op read`, host-only, container has no `op`). Card fields resolve by **UUID ref** (title refs fail on the parens): `op://OpenClaw/unzgepzoawseipfutwccfgpffq/{cardholder,ccnum,cvv,expiry}` — verified display-immune (ccnum len16, cvv len3, expiry len6).
- **Brief already scaffolds it.** `PA-PLAYBOOK §Payments` anticipates graduation ("Peter completes … or, once graduated, approves me to"); §Accounts already defines op-get login-retrieval, op-put credential-save, and account-creation, all L1-gated. Browser posture is already Tier-1 host Chrome, **no site allowlist**, SSRF refuses only private IPs.
- **Cap is at the issuer.** £200/mo lives on the card ([P6-45]), not agent logic. finance.sqlite is a memory index, **not** a ledger — the "Finance log" is a reconciliation record we append to.

## 3. Obsolete assumption to retire
The deferred L3+ block in PA-PLAYBOOK still says auto-pay must **provision the card into `auth-profiles.json`** "because F70 (can't read 1P unattended)." **[F70] is resolved** ([P6-88], service-account op-get). → The card is **host-injected via op-get at point-of-use**; **no** `auth-profiles.json` copy, no second copy anywhere.

## 4. The flow (target)
1. **Login (once per site, then persists).** Host fills the site's credential from op-get into the login form of the `openclaw` profile (or, for bot-sensitive / SSO+2FA sites like Peter's own Google-SSO Anthropic account, a one-time operator login). Session persists → reused thereafter. Model never handles the credential. Missing credential → Marie surfaces the need (or creates an account: op-generated password, op-put → "Agent-Provisioned" vault).
2. **Checkout.** Marie drives the merchant to the **payment screen** on the Tier-1 host browser, reads the **real total** off the page.
3. **Surface + approve.** Marie **pauses** and surfaces a complete proposal — payee, amount+currency, what-for, which card, checkout link/screenshot. She does **not** proceed until Peter approves.
4. **Card inject (host).** On approval, the **host** resolves the card via op-get and fills the card fields into the same tab (mechanism §5). Card value never enters Marie's sandbox/context.
5. **Pay + 2FA.** Submit → **Revolut 2FA pause** → Peter approves in the Revolut app → charge completes.
6. **Finance log.** Append the transaction to the reconciliation ledger (§6).

Autonomy ladder reinterpreted per Peter's model: the graduation is **autonomous login + checkout** (no per-login involvement); **the payment itself is always Peter-approved + 2FA** (£200 issuer cap as backstop). No silent auto-pay.

## 5. Card-inject mechanism — the one real design fork
`fill`/`click` need a **`ref` from a `snapshot`**; CSS selectors are unsupported; the **CLI has no `snapshot`** (agent-tool only). Options:
- **(A) agent-snapshot → host-fill by ref.** Marie snapshots the payment page (she sees refs, not values), surfaces the card-field refs; host runs `openclaw browser fill --fields '[{"ref":"<ccnum>","value":"…"}]'` per field with op-get values. Clean trust-wise; needs agent→host ref handoff.
- **(B) host `evaluate`.** Host runs a JS fill against the whole page (no ref, not SSRF-gated), locating fields by `autocomplete` (`cc-number`/`cc-exp`/`cc-csc`) with native-setter + input/change events. Self-contained; more fragile per-site; **secret-hygiene wrinkle** — must keep the card value off `argv`/temp files (pass via stdin/env to the evaluate call, never a workspace file).
- **Recommendation:** **(A)** as primary (cleaner boundary, robust), **(B)** as fallback for pages where refs are awkward. Either way the **host** performs the fill; the value's DOM lifetime is bracketed by the pay-submit.

## 6. Finance ledger (reconciliation)
Append-only markdown (proposed `~/.openclaw/shared/finance-ledger.md`, shared so the `finance` agent can later own reconciliation): one row per settled charge — date, payee, amount+ccy, what-for, card (last-4 only, never full PAN), merchant URL, Peter-approval msg ref. Never the full card number.

## 7. Trust boundary — **the item needing Peter's sign-off**
- **op is host-only**; card + credentials never enter the sandbox or the model's context. ✓ by design.
- **Residual risk:** the agent controls the *same* host tab the host fills into, so a compromised/curious agent turn could `snapshot`/`evaluate` and read a filled field's `.value`. **Mitigation:** card handling happens **only while the agent is paused** (it surfaced and is awaiting Peter) — the host fills + Peter drives the pay/2FA; the agent takes **no browser-read turn between fill and submit**. Card is never present in the DOM while Marie is "thinking." Optionally fill+submit in one host action to minimise DOM lifetime.
- **Confirm:** is this boundary acceptable, or do you want a stronger guarantee (e.g. a dedicated non-agent browser context for the pay step)?

## 8. Build plan / status
- [x] Recon (browser sharing, allowHostControl, op-get card, brief, finance) — **done**.
- [x] `scripts/op-list.sh` (display-immune 1P structure lister) — **done**.
- [ ] `scripts/pay-fill.sh` — host card-inject helper (ref-based fill; op-get by UUID; value off argv). **Reversible/inert until called.**
- [ ] Finance ledger scaffold (§6).
- [ ] **Graduate PA-PLAYBOOK/AGENTS §Payments + §Accounts** — retire auth-profiles.json; encode the §4 flow; **staged as a diff for Peter's review** (live bind → applies to running Marie).
- [ ] **Establish persistent logins** (needs Peter for SSO/2FA sites, e.g. Anthropic Console).
- [ ] **F26 end-to-end** via a real agent turn on a **Peter-directed real purchase** (needs Peter present for 2FA; needs a **cost OK** for the test agent turn(s), [P6-51]).

## 9. Gates that need Peter
1. **Trust-boundary sign-off** (§7).
2. **Card-inject mechanism** pick (§5: A primary / B fallback — or his preference).
3. **Cost authorization** for the test agent turn(s) to validate the fill on a live checkout ([P6-51] — cheapest model, ~$5 class, no loops).
4. **One-time SSO login** for his own accounts (Anthropic) + presence for the Revolut 2FA on the F26 charge.
