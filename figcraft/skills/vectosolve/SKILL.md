---
name: vectosolve
description: VectoSolve AI image tools — vectorize, remove background, upscale, generate SVG logos — via the @vectosolve/mcp MCP server (or the vectosolve CLI). The asset-generation arm of the FigCraft playbook: produces and cleans artwork that then flows through the AGENTS.md §5 workflow (render → verify → rasterize → place). Paid API — the credit rules are not optional.
---

# VectoSolve — AI image tools (asset pipeline)

> A paid image-processing API exposed over MCP (`npx @vectosolve/mcp`) and CLI (`npx vectosolve`). Four operations; results feed the FigCraft asset workflow. Every call costs real money — see §3.

## When to use

The user asks to vectorize an image, remove a background, upscale a low-res asset, generate a logo or icon variants, or "pakai vectosolve". Especially: producing illustration assets destined for a `.figcraft` document.

## 1. Setup

1. API key: create an account at <https://vectosolve.com>, then create a key at <https://vectosolve.com/developers>. **First 10 API calls are free, no card** (vectorize, remove_background, upscale only — logo generation is paid from the start). Beyond that, buy credits or subscribe; one key works across API, MCP, SDK, and CLI.
2. Register the MCP server (Claude Code, user scope):

   ```sh
   claude mcp add --scope user vectosolve --env VECTOSOLVE_API_KEY=vs_xxx -- npx -y @vectosolve/mcp
   ```

   Node ≥ 18. Verified package: `@vectosolve/mcp` (1.0.3, MIT, bin `vectosolve-mcp`). Tarball audited 2026-10-19: no install scripts, no `child_process`/`eval`, writes nothing to disk, network calls go only to `vectosolve.com/api/v1` (overridable via `VECTOSOLVE_BASE_URL`). Re-audit when the version moves.
3. CLI alternative (batch/CI without an MCP client): `npx -y vectosolve@latest …` or `npm i -g vectosolve` — same key via `VECTOSOLVE_API_KEY`, same credit balance. Discover its command surface with `vectosolve --help`; the SDK (`@vectosolve/sdk`) covers app integration.

## 2. The four tools

| Tool | Does | Input | Output | Cost |
|---|---|---|---|---|
| `vectorize` | raster → clean SVG | PNG/JPG/WebP | SVG content | $0.20 |
| `remove_background` | cut out the subject | any image | transparent PNG **URL** | $0.07 |
| `upscale` | AI upscaling | low-res image | higher-res image | $0.15 |
| `generate_logo` | text prompt → logos | description | SVG variants | $0.40 (recraftv3) / $0.25 (recraftv2) |

Every tool accepts **`file_path`** (local file) or **`image_url`** (public URL).

### `generate_logo` parameters

- `prompt` — what to generate (include the brand name)
- `style` — `vector_illustration` | `icon` | `line_art` | `engraving` | `line_circuit` | `linocut`
- `model` — `recraftv3` (best quality) or `recraftv2` (cheaper)
- `colors` — brand palette as a hex array, e.g. `["#0090ff", "#1cb721"]`
- `num_variants` — 1, 2, or 4 (no other values)

## 3. Credit rules (paid API)

1. **Estimate before you spend.** State the cost (count × price) for anything beyond a single call. Ask before batch runs and before `num_variants: 4`.
2. **Report usage.** Every response includes credit usage and remaining balance — relay both to the user with the result.
3. Credits are shared across MCP, CLI, SDK, and raw REST (`POST https://vectosolve.com/api/v1/*`, Bearer auth, rate limit 60 req/min) — one key, one balance. The free 10 calls cover vectorize/remove_background/upscale only.

## 4. FigCraft integration

Mind the format split — figcraft's `image.place` is **raster-only** (AGENTS.md gotcha 4):

| VectoSolve output | Into FigCraft |
|---|---|
| `generate_logo` / `vectorize` SVG | **(a)** rasterize (`qlmanage -t -s 512` / `rsvg-convert -w 512`) → `image.place`, or **(b)** `file.open` the SVG as an editable vector document |
| `remove_background` PNG | raster already — download it, then `image.place` |
| `upscale` image | raster — `image.place` directly |

Pipeline tips:

- **upscale → vectorize**: upscale small/low-res sources first; tracing a 64px favicon yields garbage.
- **remove_background → upscale** for product-photo placements (figcraft embeds placed images; 512px is ample for a 160–240px slot).
- Reuse the project palette in `colors` and record it in the project (the §5 style-consistency rule).
- After placement, run the figcraft §6 verification loop (export/screenshot, then look at it).

## 5. Asset verification (always)

Generated ≠ correct. Same rule as figcraft §5: **render and look before trusting**.

1. Logos/illustrations: rasterize the SVG and vision-check the render — misplaced shapes and same-color-on-same-color invisibility don't show in the markup.
2. Cutouts: view the result on a contrasting background to spot halo and edge artifacts.
3. Iterate (sharper prompt, different `style`) **before** placing — a bad asset placed into the document still costs a fix cycle there.

## 6. Gotchas

1. **`remove_background` returns a URL**, not a file — download it into the project before placing; assets living only in chat responses can expire with the link.
2. **`num_variants` accepts only 1, 2, or 4.**
3. **SVG outputs can't go straight into `image.place`** (`not a valid FigCraft file: unsupported image`) — rasterize first or `file.open` as a vector document.
4. **The CLI shares the key and the balance** — installing it grants no extra credits.
5. **A 4-variant recraftv3 logo run is $1.60** — the priciest single action here; confirm before spending.
6. **Your images leave the machine** — every call uploads the file to VectoSolve's cloud for processing. Confirm with the user before sending confidential or client artwork.
7. **The API key sits in plaintext** in `~/.claude.json` (user scope). Never commit a real key in a project `.mcp.json`; rotate it at vectosolve.com/developers if it leaks.
8. **Rate limit: 60 requests/minute** — larger batch runs must honor `Retry-After` / `X-RateLimit-Remaining` instead of hammering the API.
