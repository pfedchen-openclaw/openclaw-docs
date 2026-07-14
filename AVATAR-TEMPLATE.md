# AVATAR-TEMPLATE — OpenClaw agent portrait system
_Canonical, reusable template for every agent avatar. Locked style; per-agent variables. Supersedes the P6-7 pixel-art descriptor (medium + structure). Generated in Gemini with the most recent locked sibling portrait attached as a strict style reference._

## How to generate a new agent's avatar
1. Take the Master Prompt below. Edit **only** the **Subject** and **Attire & Accessories** sections (hair, skin, expression; jacket/blouse; the letter pin). Keep **Style** and **Composition** verbatim.
2. In Gemini, attach the most recent **locked** sibling portrait (currently **Charlotte**) as a style reference, leading with: _"Match the attached reference's art style, line weight, gradients, flat shading, colour palette, framing, lighting, proportions, and warm-cream background exactly — this is a sibling portrait in the same set; change only the features described below."_
3. Set on the Mini: BotFather `/setuserpic` (+ `/setname`, `/setdescription`) and OpenClaw `agents set-identity`.

## Master Prompt (Charlotte — current locked exemplar)
**Subject:** High-resolution 2D vector-art avatar of a poised female executive assistant. Centered head-and-shoulders portrait, facing forward. Fair skin, confident smile, shoulder-length chestnut-brown hair with volume and wavy, outward-flaring ends.
**Attire & Accessories:** Dark navy-blue suit jacket over a cream blouse with a large, neatly tied pussy-bow. Small gold stud earrings, thin gold chain necklace. Vivid red open-claw lobster brooch on her left lapel (viewer's right). Subtle gold "C" pin on the opposite lapel.
**Style:** Professional digital illustration featuring smooth gradients, clean sharp lines, and crisp flat shading. Strictly no pixel art. Clean, polished finish.
**Composition:** Solid warm-cream background. The character finishes with a smooth, rounded bust silhouette at the bottom. 1:1 square aspect ratio with generous margins designed specifically for a circular profile picture crop.

## Locked invariants (never change — this is what makes them a family)
- **Style** + **Composition** sections, verbatim.
- Warm-cream background; head-and-shoulders, forward-facing; rounded bust silhouette; 1:1 framing for a circular crop.
- **Vivid red open-claw lobster brooch** on the left lapel (viewer's right) — the shared OpenClaw brand mark, on **every** agent.
- A subtle **gold pin shaped as the agent's initial** on the opposite lapel.
- No text other than the letter; no watermark.

## Per-agent variables
- **Subject:** hair colour/style, skin tone, expression, persona read.
- **Attire:** jacket colour, blouse/shirt style, earrings/necklace.
- **The letter pin.**

## Per-agent record
- **Charlotte (ProA · "C") — LOCKED ✅** — chestnut-brown wavy outward-flaring hair; navy-blue suit; cream pussy-bow blouse; gold studs + thin gold chain; red lobster brooch; gold "C". File: `Charlotte_-_ProA.png`. (`/setuserpic` + `agents set-identity` still to apply on the Mini.)
- **Marie (PA · "M") — LIVE, pre-template** — navy-blue wavy shoulder-length bob; charcoal-grey suit; white collared shirt; single-strand pearls; gold "M". File: `Marie_-_PA.png`. Her portrait predates this template and is slightly less polished; **flagged for re-generation to the locked style** (deferred — see deviations log; the style may refine further as more agents are made, so batch the re-gen with a later agent).
- **Andreas (CoS · "A") — PAIRED + F26-passed (6c.33 [P6-145]); avatar PENDING** — first **male** portrait; German/Swiss; charcoal (or dark-navy) suit + muted tie; clean-shaven, short dark side-part; red lobster brooch; gold "A". **Ready-to-paste Gemini prompt: `AVATAR-PROMPT-ANDREAS.md`** (Style+Composition verbatim vs the Charlotte exemplar). File (planned): `Andreas_-_CoS.png`. Apply: BotFather `/setuserpic` + `/setname` + `/setdescription`, then `openclaw agents set-identity`.
- **Andreas (CoS · "A") — pending** — final docs-locked step at 6d (male, German/Swiss).
- SDR / Travel / Research / Finance / Librarian — pending.
