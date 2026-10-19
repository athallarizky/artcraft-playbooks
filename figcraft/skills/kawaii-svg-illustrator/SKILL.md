---
name: kawaii-svg-illustrator
description: Create clean, editable SVG illustrations in a warm, minimalist kawaii vector style. Use for characters, animals, people, objects, food, vehicles, mascots, app icons, stickers, and other subjects—not just cats or animals.
---
# Kawaii SVG Illustrator

## Purpose

Generate production-ready SVG illustrations using a consistent visual language inspired by warm, minimalist kawaii vector art: organic silhouettes, thick rounded dark-brown outlines, restrained pastel colors, simple expressive details, and flat layered shapes.

This is a **style system, not a subject template**. Apply it to any requested subject while preserving the subject's recognizable features. Do not automatically add animal ears, faces, blush, paws, or other character details to objects that do not call for them.

## When to use

Use this skill when asked to:
- Create or generate an illustration as SVG.
- Turn a subject or reference into a cute, simple vector illustration.
- Design a mascot, sticker, icon, character, object, or small illustration set in this style.
- Produce editable SVG source rather than a raster image.

If the user provides reference images, inspect them first and use them to infer the subject, pose, proportions, palette, and visual details. Treat the style rules below as defaults; follow explicit user requirements when they conflict.

## Style specification

### 1. Overall visual language

- Cute, friendly, calm, approachable, and uncluttered.
- Flat vector illustration with a hand-shaped, organic feel—not sterile geometric iconography.
- Use a small number of large, readable shapes. Prioritize the silhouette and recognizability at thumbnail size.
- Favor rounded forms, gentle asymmetry, and smooth curves over sharp corners.
- Keep details sparse. Each detail should improve recognition or expression.
- Aim for a finished sticker/mascot illustration, not a sketch, wireframe, or photorealistic rendering.

### 2. Outlines

Use a very dark warm brown instead of pure black.

Recommended starting tokens:
- `--ink: #3B2419` or `#3A241A`
- Main outline width: roughly 1.8–3.2% of the artboard's shorter dimension.
- Secondary interior line width: roughly 70–85% of the main outline.
- Line caps: `round`.
- Line joins: `round`.

SVG example:

```svg
<path
  d="M 20 70 C 20 35, 45 15, 75 20"
  fill="none"
  stroke="#3B2419"
  stroke-width="8"
  stroke-linecap="round"
  stroke-linejoin="round"
/>
```

Rules:
- Keep outline weight visually consistent across the illustration.
- Use thinner interior lines only when needed to describe limbs, seams, folds, or small details.
- Avoid hairline strokes, black outlines, sharp miter joins, and excessive interior contour lines.
- Do not outline every color patch. Use outlines to define the main silhouette and meaningful separations.
- Avoid doubled strokes at joins. Where possible, use one continuous path for a contour.
- For small facial details, use a slightly lighter visual weight than the outer silhouette, but keep them bold enough to read.

### 3. Color palette

Use a warm, low-saturation palette. These values are starting points, not mandatory fixed colors.

| Token | Suggested value | Use |
|---|---|---|
| Ink | `#3B2419` | Main outlines, eyes, mouth, key details |
| Warm background | `#FFF2DF` | Canvas/background |
| Cream | `#FFF8EC` | Light body surfaces |
| Soft ivory | `#FFF7E9` | Highlight or primary light fill |
| Caramel | `#B9825B` | Main warm midtone |
| Toasted brown | `#A96F4C` | Darker warm patches |
| Light tan | `#EAD0A7` | Soft underside/shadow |
| Peach | `#F2CFAE` | Blush or warm accents |
| Soft pink | `#F3A0A0` | Optional pink accents |
| Golden orange | `#F6AD4E` | Optional bright accent |

Palette rules:
- Usually use 3–6 colors plus the outline.
- Keep the background warm and light unless the user requests transparency or another background.
- Prefer flat fills. Use no gradients by default.
- Use one or two simplified shadow shapes in a related warm tone only if they improve form.
- Avoid pure white and pure black when a warm off-white or dark brown works better.
- Preserve requested brand colors or reference-specific colors when needed, while keeping the same shape and outline language.
- If the subject naturally needs other colors (e.g. a blue bus or green plant), choose soft, slightly warm versions rather than forcing everything to brown.

### 4. Shapes and silhouettes

- Build the illustration from clear, closed silhouettes using `<path>`, `<ellipse>`, `<circle>`, and `<rect>`.
- Prefer cubic Bézier curves (`C`, `c`) and quadratic curves (`Q`, `q`) for soft, organic contours.
- Use arcs only when a true circular or curved form benefits from them.
- Avoid overcomplicated paths with many unnecessary anchor points. Use the fewest points that produce a clean contour.
- Favor slightly oversized heads or focal elements for characters, but adapt proportions to the subject.
- Keep appendages and small features chunky enough to remain visible at the final display size.
- Simplify tiny mechanical, anatomical, or decorative details rather than reproducing every real-world feature.
- Use intentional asymmetry to make the art feel warm and lively, but maintain visual balance.

### 5. Layering and depth

Construct SVG in a deliberate back-to-front order:

1. Background (if requested).
2. Rear parts: tails, handles, stems, back limbs, hair behind the head, or other elements behind the main body.
3. Main body silhouette.
4. Large color patches and secondary fill shapes.
5. Face or primary identifying details.
6. Front parts: hands, paws, accessories, foreground limbs, and finishing contours.
7. Small highlights and optional decorative marks.

Layering rules:
- Place rear elements first so the main silhouette naturally covers their roots.
- Use a consistent outline on major forms, but avoid showing internal borders where two parts should merge.
- Use fill shapes behind the main contour when possible to avoid visible seams.
- If a patch touches the silhouette edge, ensure its boundary is intentional and does not accidentally create a second outline.
- Prefer clipping paths or well-designed enclosed shapes over awkwardly overlapping shapes.
- Keep negative space clear; do not let facial details, limbs, or accessories collide unintentionally.

### 6. Face and expression (only when appropriate)

For characters, mascots, and personified objects:
- Eyes can be dark, simple circles or softly rounded ovals.
- Add a small off-white highlight to one side of each eye if it helps the expression.
- Use a tiny nose and a simple curved mouth, often made from one or two strokes.
- Optional blush uses soft peach or pink circles/ellipses with no outline.
- Keep spacing and symmetry intentional. Slight differences can make the face feel more alive.
- Expression should be readable at small sizes.

Do not add a face to ordinary objects unless personification is requested or clearly appropriate. For people, use the same simplified approach without erasing defining requested features such as hairstyle, glasses, clothing, or pose.

### 7. Shading and highlights

- Default to flat color with little or no shading.
- When needed, add a small number of flat shadow shapes in a warm, adjacent hue.
- Shadows should describe broad form (e.g. underside, overlapping cheek, body edge), not simulate realistic lighting.
- Avoid blur filters, complex gradients, glossy 3D highlights, noise, textures, and drop shadows unless requested.
- Highlights should be minimal, crisp, and intentional.

### 8. Composition and canvas

- Use a `viewBox`, normally `0 0 512 512`, unless another aspect ratio better suits the subject.
- Set explicit `width` and `height` when a target size is specified; otherwise a scalable `viewBox` is sufficient.
- Keep the subject comfortably inside the canvas, with even visual breathing room.
- Center the visual mass, not necessarily the mathematical bounding box.
- Avoid cropping ears, tips, wheels, accessories, or other important silhouette features unless the user asks for a close crop.
- Use a plain warm background by default for a complete illustration. For an icon, sticker asset, or transparent-background request, omit the background rectangle.
- For a set of illustrations, maintain consistent outline width, palette logic, detail density, and scale.

## SVG implementation rules

1. Return valid, self-contained SVG markup.
2. Include `xmlns="http://www.w3.org/2000/svg"` on the root `<svg>`.
3. Use a meaningful `viewBox`; avoid hard-coding every shape to pixel dimensions.
4. Prefer native SVG primitives and paths. Do not embed a raster image or use `<foreignObject>` to fake an SVG illustration.
5. Use readable IDs for reusable definitions, such as `clip-body` or `outline`.
6. Use `<defs>` for reusable clipping paths or symbols only when they genuinely improve clarity.
7. Avoid filters, masks, clipping, or transforms that are more complex than the illustration requires.
8. Keep the SVG source formatted and grouped by semantic layers with comments.
9. Use `fill="none"` explicitly for stroke-only paths.
10. Do not rely on external fonts, linked images, CSS frameworks, scripts, or external assets.
11. Use a `<g>` for shared stroke attributes where it makes the markup simpler, but do not make the source difficult to inspect.
12. Avoid unnecessary metadata and editor-specific markup.

Suggested root:

```svg
<svg xmlns="http://www.w3.org/2000/svg"
     viewBox="0 0 512 512"
     role="img"
     aria-labelledby="title desc">
  <title id="title">Illustration title</title>
  <desc id="desc">Short description of the illustrated subject.</desc>
  <!-- Optional background -->
  <!-- Rear shapes -->
  <!-- Main silhouette -->
  <!-- Color patches and details -->
</svg>
```

## Workflow

Follow this process for each request.

### Step 1 — Understand the brief

Identify:
- Subject and its defining features.
- Desired pose, view angle, and expression (if applicable).
- Intended use: icon, sticker, mascot, avatar, illustration, or asset set.
- Background requirements: warm solid, transparent, or custom.
- Output constraints: viewBox, dimensions, colors, or SVG-only.
- Any reference image or existing SVG that must be followed.

If an essential detail is missing, make a sensible design decision instead of asking multiple questions. Ask one concise clarification only when the ambiguity materially changes the output.

### Step 2 — Reduce the subject to visual primitives

Before writing SVG, describe the subject as a small layer plan:
- Main silhouette.
- Secondary silhouette(s).
- Distinctive color regions.
- Important internal lines.
- Optional face/accessories.
- Background or decorative elements.

Preserve the few traits that make the subject recognizable. Remove low-value detail.

### Step 3 — Establish design tokens

Choose:
- Main outline color.
- Outline width based on the viewBox.
- Background and base fill.
- 1–4 secondary colors.
- Optional shadow and highlight colors.

Keep tokens consistent throughout the asset.

### Step 4 — Draw back to front

Build major closed shapes first, then add patches, inner contours, and details. Make paths simple and deliberate. Ensure the outline describes the subject clearly even if the fills are temporarily hidden.

### Step 5 — Check visual quality

Inspect the SVG by rendering it when a renderer is available. If possible, preview at both the intended size and a smaller thumbnail size.

Check:
- Is the subject immediately recognizable?
- Is the silhouette balanced and fully inside the canvas?
- Are stroke widths consistent and joins rounded?
- Are there accidental gaps, overlaps, doubled outlines, or awkward tangencies?
- Do color patches follow the form?
- Is the negative space clean?
- Are small features large enough to remain visible?
- Does the illustration use too many details or colors?
- Is the style consistent with the warm, soft, rounded reference?
- Is the SVG valid and self-contained?

Fix visible issues before returning the final result. Do not claim to have rendered or validated it if no rendering/validation was actually performed.

### Step 6 — Deliver

By default, provide:
- The complete SVG source in a fenced `svg` code block, or save it as an `.svg` file if the environment supports file creation and the user requests a file.
- A short note about the viewBox and whether the background is included.
- Any relevant usage notes, such as how to make the background transparent.

If the user asks for SVG only, return only the SVG source or file.

## Quality checklist

Before finalizing, confirm:

- [ ] The subject remains recognizable.
- [ ] The silhouette uses smooth, organic curves.
- [ ] The outline is dark warm brown, thick, and rounded.
- [ ] The palette is restrained, warm, and coherent.
- [ ] Shapes are flat and clean; shading is minimal.
- [ ] Layers overlap in a logical back-to-front order.
- [ ] No unwanted sharp corners, hairline strokes, or excessive details.
- [ ] The composition has appropriate breathing room.
- [ ] The SVG is self-contained and uses no external assets.
- [ ] The code is readable and grouped by semantic layer.
- [ ] The style is applied to the requested subject rather than copying an unrelated subject's features.

## Common failure modes to avoid

- Using pure black strokes when warm dark brown is more appropriate.
- Using thin outlines that make the result feel like a generic icon set.
- Overusing gradients, shadows, texture, or 3D effects.
- Making every shape perfectly symmetrical and mechanically smooth.
- Adding random blush, eyes, ears, paws, or kawaii facial features to unrelated subjects.
- Drawing lots of tiny details that disappear at small sizes.
- Letting multiple outlines overlap and create dark knots.
- Using too many colors or high-saturation neon accents.
- Placing the subject too close to the canvas edge.
- Producing a complicated path that is difficult to edit when simpler shapes would work.
- Embedding a raster reference inside the SVG instead of recreating it with vector geometry.

## Adaptation by subject

Use the same style language while changing the construction strategy:

- **Animals / creatures:** soft body silhouette, simplified limbs, distinctive ears/tail/beak/horns, minimal face.
- **People:** rounded proportions, simplified hair and clothing silhouettes, restrained facial details, readable pose.
- **Food:** clear outer contour, a few defining toppings or layers, minimal highlights.
- **Objects / tools:** rounded corners and simplified functional parts; preserve the object's recognizable shape.
- **Vehicles:** chunky rounded body, large readable wheels/windows, very few mechanical details.
- **Plants / nature:** organic leaf shapes, gentle curves, simple stem and pot silhouettes.
- **Buildings / places:** simplified large shapes, rounded corners where appropriate, limited windows and structural details.
- **UI icons / symbols:** reduce detail further; retain the same outline and palette, and ensure readability at small sizes.
- **Mascot sets:** maintain consistent stroke, eye language, palette logic, and proportions across all assets.

The defining rule: **simplify the subject, not its identity**. The result should feel warm, readable, cohesive, and intentionally drawn.
