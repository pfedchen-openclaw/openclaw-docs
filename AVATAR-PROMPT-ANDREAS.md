# Avatar generation prompt — Andreas (CoS · "A")

_Ready-to-paste Gemini prompt. First **male** portrait in the set. Derived from AVATAR-TEMPLATE.md
(Style + Composition **verbatim**; only Subject/Attire/letter changed) + BRIEF-DERIVATION-ANDREAS §(e).
Captured 6c.33 so it isn't lost; generate + apply at any convenient point (cosmetic, non-blocking).
Andreas is already paired + F26-passed ([P6-145]) — the avatar is the only outstanding pairing polish._

## Step 1 — attach the reference
In Gemini, attach the locked **Charlotte** exemplar (`Charlotte_-_ProA.png`) as a strict style
reference, then paste the prompt below. (Charlotte is the current locked sibling; keep her attached
so the family style/line-weight/palette/lighting/cream background carry across.)

## Prompt
> Match the attached reference's art style, line weight, gradients, flat shading, colour palette, framing, lighting, proportions, and warm-cream background exactly — this is a sibling portrait in the same set; change only the features described below.
>
> **Subject:** High-resolution 2D vector-art avatar of a poised, strategic male chief of staff — an early-40s executive with calm authority (a former management-consultant / chief-of-staff read). Centered head-and-shoulders portrait, facing forward. Fair-to-light skin; a composed, confident, assured expression (self-assured, not stern); short, neatly-groomed dark hair in a clean side-part; clean-shaven. German/Swiss persona.
>
> **Attire & Accessories:** Charcoal (or dark navy-blue) tailored suit jacket over a crisp white (or pale-blue) shirt, with a muted, subtly-knotted professional tie; optional small tie-bar; no earrings. Vivid red open-claw lobster brooch on his left lapel (viewer's right). Subtle gold "A" pin on the opposite lapel.
>
> **Style:** Professional digital illustration featuring smooth gradients, clean sharp lines, and crisp flat shading. Strictly no pixel art. Clean, polished finish.
>
> **Composition:** Solid warm-cream background. The character finishes with a smooth, rounded bust silhouette at the bottom. 1:1 square aspect ratio with generous margins designed specifically for a circular profile picture crop.
>
> No text other than the gold letter "A"; no watermark.

## Locked invariants (do not change — this is what makes the set a family)
Warm-cream background · head-and-shoulders, forward-facing · rounded bust silhouette · 1:1 for a
circular crop · **vivid red open-claw lobster brooch** on the left lapel (viewer's right) · a subtle
**gold "A"** pin on the opposite lapel · no text but the letter, no watermark. Style + Composition
sections are verbatim from the Charlotte exemplar.

## Step 2 — apply on the Mini
Save as `Andreas_-_CoS.png`, then BotFather **`/setuserpic`** + **`/setname`** (Andreas) +
**`/setdescription`**, then **`openclaw agents set-identity`** for `cos`.

## Optional tuning
- Beard: prompt says **clean-shaven** (reads more distinguished at a small circular crop vs the
  siblings); swap to "light, neatly-groomed stubble" if preferred.
- Suit: **charcoal** is the primary read (dark-navy is Charlotte's) — leading with charcoal keeps
  Andreas visually distinct from the sibling in navy.
