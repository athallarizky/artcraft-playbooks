---
name: pendev-mascot-svg
description: Generate standalone flat/kawaii vector mascot SVG files by writing SVG markup directly — no image generation service or pen.dev dependency required. Use when asked to create a mascot, character, chibi avatar, or simple flat illustration as an .svg file. If a dedicated vector generation tool IS available (e.g. pen.dev Generate), prefer it — this skill is the no-dependency fallback.
---

# Mascot SVG — portable flat mascot generator

A tested reference output lives at `examples/sapi.svg` in this skill folder — read it before generating your first mascot, and treat it as the baseline quality bar for the style anchor, palette, silhouette–stroke trick, and layer ordering.

You (the agent) write the SVG markup yourself. Follow this discipline exactly. The goal is the "Kiko look": flat vector, chunky rounded shapes, thick uniform outline, minimal kawaii face, tiny palette. Restraint is the style — every extra detail makes it worse.

## Style anchor (the one look)

> **SUBJECT**, flat kawaii vector mascot, chunky rounded shapes, thick uniform outline, minimal face, clean sticker style

Commit to this anchor. Variants of the same character reuse the IDENTICAL anchor and change only the pose/subject. Consistency across characters comes from repeating the same style phrases, never from improvising.

## Hard rules

1. **Single self-contained file**: `xmlns="http://www.w3.org/2000/svg"`, `viewBox="0 0 512 512"`, explicit `width`/`height`.
2. **Flat fills only** — NO gradients, NO filters, NO embedded raster images, NO text elements.
3. **Max 8 colors**, drawn from the palette formula below.
4. **One uniform stroke width** (10–12 at 512px) with `stroke-linecap="round"` and `stroke-linejoin="round"`.
5. **Max ~40 shapes**; every shape must map to a recognizable body part. No decorative noise.
6. **Build back → front**, each body part in a commented `<g>` group.
7. **Padding**: keep all geometry ≥ 12px inside the viewBox; character optically centered.
8. **Symmetry**: the character is front-facing. Construct one side, mirror coordinates around the vertical axis (x' = 512 − x).

## Palette formula (keep the relationships, swap the hues)

| Role | Default | Notes |
|---|---|---|
| outline | `#3B2E2A` | dark warm, never pure black |
| body base | `#FFF9F0` | warm near-white or subject color |
| secondary (spots/mane) | `#4E403A` | same family as outline, lighter |
| accent pink (ears/muzzle/blush) | `#F7B8B4` `#FFDCCF` `#F7A8A0` | muzzle is the lightest |
| cream (horns/hooves) | `#F2E5C9` / `#C9A794` | horns light, hooves muted |

Same lightness/saturation relationships, always. A cow, a cat, a frog differ by hue + silhouette, not by rendering style.

## The silhouette–stroke trick (most important rule)

When a silhouette carries clipped markings (spots, patches):

1. draw the silhouette shape with **fill only** (no stroke),
2. draw markings inside a `<clipPath>` that duplicates the silhouette geometry so they bleed past the edge and get cut to it,
3. re-draw the same silhouette shape with **`fill="none"` + stroke** on top.

Never stroke the fill pass directly — markings would cover the inner half of the outline.

## Construction recipe (back → front)

1. ground shadow — wide ellipse, outline color at 10% opacity
2. background appendages (tail)
3. outer ears + horns — **behind** the head, bases hidden by head fill
4. body silhouette: fill pass
5. body markings clipped to body
6. body stroke pass
7. front legs + hoof details (strokeless inner shapes)
8. head: fill pass → clipped markings → stroke pass
9. muzzle (wide soft ellipse) + nostrils (small vertical ellipses) + mouth (single arc)
10. eyes + blush

Reference geometry for a 512 canvas: head ellipse ≈ cx 256, cy 196, rx 136, ry 124; body ellipse ≈ cx 256, cy 380, rx 150, ry 108 (head overlaps body top). Scale limbs to match.

## Face rules (kawaii = restraint)

- Eyes: filled circles r ≈ 13, one white highlight circle r ≈ 4.5 offset up-out. No eyebrows, no lashes.
- Mouth: one quadratic smile arc, stroke-width ≈ 8, fill none. Nothing else.
- Blush: two ellipses at ~55% opacity, placed wide on the cheeks.
- If a marking (spot/patch) would sit under an eye, shrink or move the marking — never let dark sit on dark.

## Subject adaptation table

| Subject | Signature parts |
|---|---|
| cow | small cream horns, wide pink muzzle + nostrils, dark spots (clipped), tail with tuft |
| cat | triangle ears, whiskers (3 short strokes/side), long curved tail |
| dog | floppy rounded-rect ears, muzzle + nose oval, short tail |
| bear | small round ears, muzzle patch, no tail |
| rabbit | long upright ears with inner pink, tiny muzzle, puff tail |
| panda | white base, dark ears/eye patches, minimal muzzle |

For anything else: derive the 2–3 most iconic silhouette features and keep everything else generic-chunky.

## Output contract

- Save UTF-8 `.svg` where the user asks; default `./mascots/<subject>.svg`.
- Validate XML before delivering: `xmllint --noout <file>` (or any XML parser). Fix all errors.
- Preview when a browser tool is available: open the `file://` URL and screenshot. Inspect at 100%: shapes not clipped at edges, outline uniform, character symmetric, face follows restraint rules. Fix and re-check — expect 1–2 refinement passes; hand-written coordinates are never perfect on the first try.
- If the design must land in a `.pen` document later, the same geometry maps 1:1 to `path` nodes (see the pen-dev skill).

## Quality checklist (all must pass)

- [ ] valid XML, renders without errors
- [ ] nothing touches or clips at the viewBox edge (≥ 12px padding)
- [ ] stroke width uniform on all silhouettes
- [ ] ≤ 8 colors, ≤ ~40 shapes, flat fills only
- [ ] left/right mirrored — no lopsided limbs
- [ ] face has exactly: 2 dot eyes (+highlights), 2 blush, nostrils, 1 smile arc
- [ ] markings clipped to silhouettes, outlines clean above markings
