# FigCraft Playbook

> Teach a coding agent to operate [FigCraft](https://github.com/storytold/figcraft) — the design tool — through its CLI and MCP control channel: install it, inspect and edit `.figcraft` documents headlessly, render for verification, and drive the running desktop app live so the user watches every change land on the canvas.

## What this playbook covers

- **Setup** — install `figcraft-cli` (prebuilt binary) and the desktop app
- **Two operating modes** — headless file scripting vs. live MCP attached to the running app
- **Document editing** — command scripts (`run`), the id system, key commands, property cheat-sheet
- **Assets** — generating, verifying, and placing SVG/PNG illustrations
- **Gotchas** — the non-obvious rules that will bite an agent otherwise

## Layout

| File | Creators | Purpose |
|---|---|---|
| [`AGENTS.md`](./AGENTS.md) | [athallarizky](https://github.com/athallarizky) | The operating manual — load this into the agent before any FigCraft work |
| [`skills/illustration-one-line/SKILL.md`](./skills/illustration-one-line/SKILL.md) | [Meng To](https://github.com/MengTo) | One-line illustration style — hand-written SVG spot illustrations via a no-dependency generator toolkit (MIT, vendored from [MengTo/Skills](https://github.com/MengTo/Skills); 12 more styles available upstream) |
| [`skills/illustration-flat/SKILL.md`](./skills/illustration-flat/SKILL.md) | [Meng To](https://github.com/MengTo) | Flat illustration style — bold flat shapes with the `flat-kit.mjs` generator toolkit (MIT, vendored from MengTo/Skills) |
| [`skills/illustration-ink-sketch/SKILL.md`](./skills/illustration-ink-sketch/SKILL.md) | [Meng To](https://github.com/MengTo) | Ink-sketch illustration style — wobbly ink linework with the `ink-kit.mjs` generator toolkit (MIT, vendored from MengTo/Skills) |
| [`skills/kawaii-svg-illustrator/SKILL.md`](./skills/kawaii-svg-illustrator/SKILL.md) | [athallarizky](https://github.com/athallarizky) | Kawaii SVG style guide — warm minimalist vector style, hand-authored SVG, no toolkit |
| [`skills/pendev-mascot-svg/SKILL.md`](./skills/pendev-mascot-svg/SKILL.md) | [athallarizky](https://github.com/athallarizky) | Flat/kawaii mascot generator — hand-authored SVG discipline with the silhouette–stroke trick |
| `skills/` | — | More specialized workflows planned (page templates, more illustration styles, …) |

## Quick start

```sh
# 1. Install the CLI (see AGENTS.md → Setup)
figcraft-cli --version

# 2. Inspect a document
figcraft-cli info design.figcraft

# 3. Or go live: launch the app with a control port and attach
FigCraft --control 7979 &
figcraft-cli mcp --attach 7979   # every edit appears in the app, in real time
```

Then read [`AGENTS.md`](./AGENTS.md) end to end — it is the source of truth for how an agent should behave.
