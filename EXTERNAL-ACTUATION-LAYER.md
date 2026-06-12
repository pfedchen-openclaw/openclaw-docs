# EXTERNAL ACTUATION LAYER — Design & Roadmap
_Opened 6c.1 (2026-06-06). The durable plan for how OpenClaw agents act on the outside world: **web, email, messengers, voice.** Email is done (6c.1). This locks the architecture, the hard constraints, the autonomy model, and the session sequencing for the rest, so each piece can be executed fresh rather than rushed._

## Vision (Peter)
Marie, Charlotte, and later agents should operate: **web** (read pages, click through and submit forms — e.g. hotel check-ins), **email** (done), **messengers** (Telegram + WhatsApp, to reach Peter's contacts and coordinate meetings), and eventually **voice** (call businesses, e.g. book a restaurant). Always as **disclosed assistants** ("X, Peter's AI-assistant"), never impersonating Peter, and **gated by the autonomy ladder** — external sends/submits/calls are not silent auto-actions at L1.

## Founding principles (carry from 6c.1)
1. **Pattern (P6-24):** every external capability = a bound tool/helper + a TOOLS.md entry + an autonomy gate. No bespoke wiring.
2. **Isolation (Strategy 2) stays.** Agents run in the `mode:all` sandbox with bridge egress. New capabilities run **in-container** where possible (headless browser) so the isolation boundary is preserved. Secrets stay outside the container.
3. **Autonomy threading.** Reads are low-stakes. **Any state-changing external action — submitting a form, messaging a contact, placing a call — is gated by the L-ladder.** At L1: prepare-and-surface or fill-and-confirm, never silent. PII/reputation actions graduate slowest. This mirrors email-draft-only and payments-prepare-only (P6-3).
4. **Identity.** Always disclosed as the assistant; never impersonation. Reaching a contact = "Marie, Peter's assistant," warm, not legalese.
5. **Host-bridge security.** Capabilities that need a host-side daemon (some messenger/voice bridges) expose a **minimal-privilege local API** the sandboxed agent calls — not arbitrary host access. Do not reintroduce the host exposure Strategy 2 avoided.

## Capabilities

### 1. Web read — EASY
Fetch + parse page content over the existing bridge egress. A small stdlib fetch helper (gmail_helper pattern) or OpenClaw's web tool if it runs in-container. Read-only, low risk. Unblocks: reading a tracking link, light research, extracting what a form needs.

### 2. Interactive browser — MEDIUM (the immediate gap)
Headless Chromium (Playwright/Puppeteer) in a **browser-capable sandbox image**, driven by OpenClaw's browser tool in-container. Keeps container isolation; egress already works. Unblocks: check-ins, clicking through portals, JS pages, multi-step forms.
- **Decision:** in-container headless (recommended — preserves isolation) vs host browser (SSRF-blocked today, reintroduces host exposure — avoid).
- **Verify (A-12):** confirm OpenClaw 2026.4.22's browser tool supports in-container headless and how it's invoked, before building.
- **Autonomy:** navigate/read freely; **form submission with PII = prepare-and-surface or fill-and-confirm at L1**, not silent submit.

### 3. Messengers — HARD (platform policy, not infra)
Proactive outreach to Peter's contacts is constrained by the platforms themselves:
- **Telegram:** bots **cannot initiate** a DM to an arbitrary user — the contact must message the bot first (opt-in), or we run an **MTProto userbot** on a dedicated account (more capable; ToS-grey; account-ban risk). Acting via Peter's own number is high-risk; not recommended.
  - **Decision:** opt-in-bot (clean, compliant, but contact must start) vs dedicated userbot (capable, risky).
- **WhatsApp:** compliant path = **Business API** (Meta business account; outbound to non-recent contacts requires **pre-approved message templates**; free-form only inside a 24h customer-initiated window). Unofficial path = a bridge (Baileys/whatsmeow) — **ToS violation, ban risk.**
  - **Decision:** Business-API-with-templates (compliant, constrained) vs bridge (flexible, risky). For genuinely cold coordination, **SMS/voice via a telephony provider may be more reliable than WhatsApp.**
- **Reality to set with Peter:** "message any contact anytime" is not freely available on either platform; the realistic model is disclosed-assistant messaging on opt-in/approved channels.

### 4. Voice / calls — HARDEST (latest)
Telephony (Twilio/Telnyx programmable voice) + STT/TTS + a realtime voice agent to call businesses as a disclosed assistant. Highest complexity and stakes (live representation). Needs a host/cloud webhook handler (bridge pattern). Deferred to the end.

## Sequencing (recommended)
- **6c.2** — Calendar helper (as planned; small, on existing rails).
- **6c.3** — **Web: read + interactive headless browser.** Completes the web half of P6-21 that 6c.1 left open; unblocks check-in-prep and research. Highest-value, cleanest of the actuation set.
- **6d** — CoS "Andreas" (now has email + calendar + web reads for its briefing). Not blocked by messengers/voice.
- **Phase-A (post-core-roster, higher autonomy):**
  - **A1 — Messengers.** Resolve Telegram (opt-in vs userbot) and WhatsApp (Business-API vs bridge) up front; build the chosen bridge with the minimal-privilege host API; gate outbound by autonomy.
  - **A2 — Voice.** Telephony stack; disclosed-assistant calling; strict autonomy gate.

Rationale: web/browser is read-adjacent and unblocks the most now, so it comes next. Messengers and voice carry real platform/ToS/account-safety decisions and belong to a higher autonomy phase (agents are L1 = prepare-only today), so they sequence after the roster exists and autonomy graduates.

## Status vs P6-21
P6-21's 6c.1 resolution covered **egress + Gmail only**. Web/browser/messengers/voice are tracked here and as **P6-25** in the deviations log. The browser gap is the honest still-open part of the original problem.
