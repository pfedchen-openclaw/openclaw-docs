# SESSION 6c.6 — Trusted-path host browser
_Opener · generated at 6c.5 close · single-purpose: give Marie and all agents reliable access to the sites in-container headless can't, proven pre-payment-safe on the hardest travel/hospitality sites._

**Delivers:** a host-side real-browser path (outbound-trusted) behind a minimal-privilege bridge, with a CAPTCHA-solver fallback so Peter is never asked to solve a CAPTCHA, wired to both pa and pro. **Exit =** reach the pre-payment state on BA, Trainline, Aman, Four Seasons end-to-end, payment gated to Peter.

## Stage 0 — Ledger + sanity (no mutating work until ALL-GREEN)
- Apply 6c.5's closure §7 ledger to DEVIATIONS-LOG.md — additive-only, original byte-recoverable, SHA-256 verified. Lands the browser trust-model decision (P6-3x) and any other 6c.5 rows.
- Run the read-only sanity batch (gateway up; pa+pro healthy; containers; config sha == last-anchored; op/service-account read; Claude Code on subscription). Confirm ALL-GREEN.
- Re-state standing items: SR-2 (ICS export to 1Password before any calendar write — still open); correspondence-review-and-fix before 6d; managed-remote browser deferred.

## Decision being executed (from §7 ledger, P6-3x)
Tiered trust: outbound-trusted → host real browser; inbound-untrusted → existing in-container headless, untrusted content, strict gates. In-house real browser primary; CAPTCHA-solving service a residual fallback (sees only the challenge); managed-remote deferred. Consequential-action gates unchanged (payments/account/PII = L1 prepare-and-surface).

## Stage 1 — Read-only recon (A-12; no mutations; greenlight before Stage 2)
- Inspect how 6c.4 wired the in-container headless browser — extend, don't break it.
- `--help`/schema-inspect OpenClaw 2026.4.22 for any native host/external-browser support vs building the bridge ourselves. Verify, don't assume.
- Confirm real Chrome on the Mini (presence/version); CDP availability; where a persistent profile lives; which non-admin user runs it; D20 GUI-launch constraints.
- Sanity-check the Mini's egress IP is residential (ASN) — validates the IP-reputation assumption.
- Read the current capability matrix + pa/pro TOOLS.md browser entries.
- Report findings + concrete build plan for greenlight.

## Stage 2 — Host browser service (greenlight-gated)
Real headful Chrome on the Mini, persistent warmed `--user-data-dir`, constrained non-admin user, CDP on loopback. GUI-bound launch per D20. F17 staging for any config. Sandbox-test before live.

## Stage 3 — Minimal-privilege CDP bridge (greenlight-gated)
Narrow bridge connecting a stealth-patched Playwright to the running Chrome over CDP (not Playwright-launched). Expose only navigate/click/type/scroll/screenshot/extract to the sandboxed agents — no arbitrary host access. SSRF guard: refuse private/internal/link-local ranges. Stealth: mask navigator.webdriver/CDP artifacts; verify real-Chrome TLS.

## Stage 4 — Trust routing + stealth verification (greenlight-gated)
Route agent/Peter-initiated → host browser; inbound/unknown → existing in-container headless. Verify against a bot-detection probe that the host browser presents clean (webdriver false, no CDP leak, real fingerprint/TLS).

## Stage 5 — CAPTCHA-solver fallback (greenlight-gated)
Select a solver (A-12: verify reCAPTCHA v2/v3 + hCaptcha + Cloudflare Turnstile support — evaluate 2Captcha/CapSolver/Anti-Captcha). Wire as residual fallback: detect → solver → submit token → resume; service receives only the challenge, never the session or credentials. Key stored outside the container (secrets pattern) + settings.json deny-Read. Peter-handoff only if the solver itself fails.

## Stage 6 — Wire to both pa and pro (P6-24; greenlight-gated)
Bound tool/helper + TOOLS.md entry + autonomy gate per agent. Consequential-action gates unchanged: payments/account-creation/PII = L1 prepare-and-surface; Peter confirms.

## Stage 7 — Exit tests (F26; real agent turn; pre-payment-safe)
For BA, Trainline (Verona→Milano), Aman, Four Seasons, each:
- Page loads real content (not a challenge/junk page).
- A real search/availability query returns real results (fares / room availability for given dates).
- Flow reaches the pre-payment state (passenger/guest details or checkout/login) and stops — surfaced to Peter. No payment, no account creation, no PII beyond search parameters.
- Any challenge auto-solved via the fallback and logged; never handed to Peter.
- Regression: prior ~70% still works; inbound-untrusted still routes to sandboxed headless.

Surface results. Do not proceed past any binary failure without a greenlight.

## Safety rails (whole session)
Read/search/navigate only on the test sites; surface-and-stop at pre-payment; no payment, account creation, or PII submission. Reversible build steps run without prompting; surface anything irreversible/destructive as a decision. Stop at ~63% context for clean closure; no auto-continuation past binary outcomes.

## Closure (three deliverables)
- DEVIATIONS-LOG.md updated in-place (additive) with 6c.6 findings; §7 ledger specifying rows to apply at 6c.7 Stage 0.
- SESSION-6c.6-CLOSURE.md.
- SESSION-6c.7-OPENER.md (ops & reliability pass).

Re-anchor shas; commit; report.

## Carry-forward
SR-2 (ICS before calendar write); correspondence-review-and-fix before 6d; managed-remote browser deferred (revisit only for a must-have hard-refuse site); any site that still failed the exit logged as a finding for the deferred tier.
