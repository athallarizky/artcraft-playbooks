# FigCraft Playbook

> Teach a coding agent to operate [FigCraft](https://github.com/storytold/figcraft) — the design tool — through its CLI and MCP control channel: install it, inspect and edit `.figcraft` documents headlessly, render for verification, and drive the running desktop app live so the user watches every change land on the canvas.

## What this playbook covers

- **Setup** — install `figcraft-cli` (prebuilt binary) and the desktop app
- **Two operating modes** — headless file scripting vs. live MCP attached to the running app
- **Document editing** — command scripts (`run`), the id system, key commands, property cheat-sheet
- **Assets** — generating, verifying, and placing SVG/PNG illustrations
- **Gotchas** — the non-obvious rules that will bite an agent otherwise

## Layout

| File | Purpose |
|---|---|
| [`AGENTS.md`](./AGENTS.md) | The operating manual — load this into the agent before any FigCraft work |
| [`skills/illustration-one-line/SKILL.md`](./skills/illustration-one-line/SKILL.md) | One-line illustration style — hand-written SVG spot illustrations via a no-dependency generator toolkit (MIT, vendored from [MengTo/Skills](https://github.com/MengTo/Skills); 14 more styles available upstream) |
| `skills/` | More specialized workflows planned (page templates, more illustration styles, …) |

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
