# FigCraft — Agent Operating Manual

> How an agent operates FigCraft: the design tool's CLI (`figcraft-cli`), its MCP control channel, and the `.figcraft` document format. Follow this manual exactly; it encodes verified behavior, not guesses.

## When to use

The user asks to create, inspect, or edit designs, export assets from a design tool, or "do it in FigCraft". If the FigCraft app is running with a control port, prefer **live mode** (§3.2) so the user sees changes as they happen; otherwise work headlessly on the file.

## 1. Setup (one-time)

### Install the CLI — prebuilt binary (preferred)

```sh
# From the latest GitHub release (example: v0.7.0)
curl -sLO https://github.com/storytold/figcraft/releases/download/v0.7.0/figcraft-cli-0.7.0-macos-universal.zip
curl -sLO https://github.com/storytold/figcraft/releases/download/v0.7.0/SHA256SUMS.txt
shasum -a 256 figcraft-cli-0.7.0-macos-universal.zip   # must match SHA256SUMS.txt
unzip figcraft-cli-0.7.0-macos-universal.zip
mv figcraft-cli-0.7.0-macos-universal/figcraft-cli ~/.local/bin/
figcraft-cli --version   # verify
```

Release assets include Linux (tar.gz/dep/rpm/AppImage), Windows (msi/zip), and a `figcraft-cli-<ver>-<platform>.zip` per platform. Building from source (`cargo build --release -p figcraft-cli`) works but needs a Rust toolchain — only do it if no prebuilt asset exists.

### Desktop app

Install from the release `*.dmg`. The CLI is **not** bundled with the app.

### Register the MCP server (for live mode)

```sh
claude mcp add --scope user figcraft-app -- ~/.local/bin/figcraft-cli mcp --attach 7979
```

## 2. Mental model

- Documents are `.figcraft` files: pages → frames/layers, each layer has a numeric **id**.
- Every editing action is a **command** (`{"command": id, "params": {...}}`). The same commands work in scripts, the CLI, and MCP — learn them with `figcraft-cli commands` (or `--json`).
- Two ways to execute:
  - **Headless**: `figcraft-cli run` transforms the file on disk. No window.
  - **Live**: the app runs with `--control PORT`; the CLI's MCP server attaches and drives the app's open session. The user sees every change; there is one source of truth (the app's memory, saved on demand).

## 3. Operating modes

### The sync model (read this before choosing a mode)

FigCraft has **no file watching**. The app never reloads a changed file from disk, there is no reload/revert command, and `file.open` on an already-open file only focuses the in-memory copy. There is no "auto-sync" to enable — instead, sync is achieved by **eliminating the file round-trip**: in live mode the agent drives the app directly, so there is never a second copy to reconcile.

| Situation | Sync behavior | Agent obligation |
|---|---|---|
| Headless, app closed (or file not open in app) | The file on disk is the only copy — nothing to sync | None; when the user opens it they see the disk version |
| Headless while the app has the same file open | **Forbidden** — two diverging copies; the app's next save silently overwrites your edit | Do not edit the file. Either switch to live mode or have the user close without saving first (§3.3) |
| Live mode (`--control` + attach) | Edits stream into the app in real time; user watches them land | `save_file` (or user ⌘S) to persist; verify by re-reading headlessly |

Rule of thumb: **one source of truth per document** — the file (headless, app closed) *or* the app's live session. Never both. If the user wants to see changes as they happen, that requirement alone decides the mode: live.

### 3.1 Headless (file on disk)

```sh
figcraft-cli run script.jsonl --in design.figcraft --out design.figcraft
```

- Prints one JSON line per step: `{"command":"frame.create","ok":true,"result":{"id":3},"step":0}`.
- A failing step prints `"ok": false` and **rolls back everything** — nothing is saved. Exit 1. Bad arguments exit 2.
- `--out` may equal `--in` (read fully, then write).

CLI surface:

| Command | Purpose |
|---|---|
| `info <file> [--json]` | Pages, node counts, top-level frames, image/style/variable counts |
| `export <file> -o <out>` | png/jpg/svg/webp/pdf/`.fig`; `--scale`, `--node ID\|NAME`, `--page NAME`, `--bleed 3mm --crop-marks --cmyk` |
| `render <file> -o <out.png>` | PNG render; `--scale`, `--page` |
| `convert <in> <out>` | Format conversion by extension |
| `run <script> [--in] [--out]` | Execute command scripts (below) |
| `commands [--json] [--filter TEXT]` | Discover every command + params |
| `mcp [--attach PORT]` | MCP server on stdio (headless, or attached) |
| `plugin publish` / `perf` | Publish plugins; benchmark |

Every file-reading subcommand also accepts Figma REST API JSON (response of `GET /v1/files/:key` written to a `.json`) and `.fig` (best effort; warnings as `note:` lines).

### 3.2 Live (attached to the running app)

```sh
FigCraft --control 7979 &          # app must be (re)started with a control port
figcraft-cli mcp --attach 7979     # MCP server on stdio driving that app
```

- The port is **fixed and arbitrary** — you choose it at `--control` and must pass the identical number to `--attach`. Not dynamic, no discovery. It listens on 127.0.0.1 only.
- MCP tools: `run_command`, `batch` (several commands = one undo step), `create_node`, `set_properties`, `inspect_document`, `get_node`, `select`, `export`, `screenshot`, `open_file`, `save_file`, `undo`, `redo`, `pointer`, `key`, `type_text`, `inspect_ui`, `list_commands`.
- Speaking MCP manually (no MCP client configured): JSON-RPC over the process's stdio — `initialize` → `notifications/initialized` → `tools/call`. Each response arrives as one JSON line per request id.

**Live-mode rules**

1. Edits land in the app's memory only — call `save_file {path}` (or let the user ⌘S) to persist. Verify a save by re-reading the file headlessly (`figcraft-cli info`).
2. Never edit a file on disk while the app holds an unsaved copy of it — the app's next save silently overwrites your edit. If the app shows the file, drive it through MCP instead.

### 3.3 The stale-copy hazard (applies to headless mode)

The app does **not** watch files on disk, has no reload command, and `file.open` on an already-open file just focuses the in-memory copy. If you edit a file the app has open, the user must close it **without saving** and reopen. When possible, avoid the situation entirely: use live mode.

## 4. Editing documents

### Script format

JSON array, `{"commands": [...]}`, or JSONL (one step per line; blank lines and `//` comments allowed):

```jsonl
// build a login page
{"command": "frame.create", "params": {"preset": "iPhone 16", "name": "Login", "props": {"fill": "#FFFFFF", "layout": "vertical", "padding": [40, 24, 32, 24], "gap": 16}}}
{"command": "text.create", "params": {"text": "Welcome back", "parent": 4, "props": {"fontSize": 28, "fontStyle": "Bold", "fill": "#111827"}}}
```

### The id system (read this twice)

- Ids number **every node in creation order**, continuing from existing nodes: in a fresh document page 1 is id 1, the first layer you create is 2. In a document whose last node is 14, your first new layer is 15.
- Compute ids by counting your own steps in order; reference earlier layers by their assigned id (`"parent": 7`).
- A `parent` that points at a non-container (e.g. a text node) fails with `can't contain layers` — recount before retrying.
- In live mode, prefer reading ids from `inspect_document` / `get_node` instead of predicting them.

### Core commands

| Command | Params |
|---|---|
| `frame.create` | `preset?` (names from `frame.presets`), `x?, y?, width?, height?, parent?, name?, props?` — without x/y a top-level frame lands to the right of existing frames |
| `frame.presets` | lists presets (Phone/Tablet/Desktop/Paper/Social…) |
| `text.create` | `text, x?, y?, parent?, width?, props?` |
| `node.create` | `type` (rectangle/ellipse/…/vector/text/frame/component), geometry, `props?` |
| `image.place` | `path` **or** `data` (base64), `x?, y?, width?, height?, parent?, mode?` — raster only |
| `node.set` | property writes (cheat-sheet below) |
| `node.reorder` | `id, index` |
| `layout.add` / `layout.remove` | `ids, direction` |
| `template.list` / `file.newFromTemplate` | start from a template |
| `file.open`, `file.save` | document-level ops (live mode) |

### Property cheat-sheet (`props`, via `node.set`)

- **Paint**: `fill "#hex"`, `fills` (paints/gradients `{type: "linearGradient", stops: [...]}`), `strokes`, `strokeWeight`, `opacity`
- **Shape**: `cornerRadius` (n or `[tl,tr,br,bl]`), `rotation`, `clip`
- **Auto layout**: `layout` (`horizontal|vertical|grid|null`), `gap`, `crossGap`, `padding` (n or `[t,r,b,l]`), `mainAlign` (`min|center|max|spaceBetween|…`), `crossAlign` (`min|center|max|baseline`), `wrap`, `sizingH/sizingV` (`fixed|hug|fill`), `absolute` (opt out of layout)
- **Text**: `fontFamily`, `fontStyle`, `fontSize`, `lineHeight`, `letterSpacing`, `textAlign`, `textCase`, `maxLines`, `fill` (text color)
- **Grid**: `gridColumns`, `columnTracks`, `gridColumn`, `gridColumnSpan`

Not every prop applies to every type — the per-step error names the problem; adjust and rerun (rollback makes retries free).

## 5. Assets (illustrations)

Workflow: **author SVG → render → vision-verify → iterate → rasterize → place.**

For AI-generated or AI-cleaned assets (logo generation, vectorize, background removal, upscaling), start with the [vectosolve skill](./skills/vectosolve/SKILL.md) (paid API), then rejoin this workflow at the render/verify step.

1. Hand-author flat-design SVG (pure shapes, no external deps, `viewBox` 512×512 works well).
2. Render a preview: macOS `qlmanage -t -s 512 -o <dir> file.svg`; Linux `rsvg-convert -w 512`.
3. **Vision-verify the render** — never trust unrendered SVG. Ask a vision model to describe it and flag misplaced shapes/ambiguities.
4. Fix and re-render until clean. Common failure: a part drawn in the **same color inside a same-colored silhouette is invisible** (e.g. a trunk over a same-shade head). Give overlapping anatomy a distinct shade or let it emerge past the silhouette edge.
5. Rasterize to PNG (512px is ample for a 160–240px placement) — `image.place` accepts raster only; SVGs import as vector *documents* via `file.open` instead.
6. Place: `{"command": "image.place", "params": {"path": "cat.png", "parent": 3, "width": 160}}`; reposition with `node.reorder` (index within parent) or `transform.moveTo`. Images are **embedded** into the document (hash returned; source file no longer needed).

Keep asset sets stylistically consistent (shared background, accent, and face conventions) — record the palette in the project and reuse its slots.

## 6. Verification loop (always)

After any edit batch:

1. Headless: `figcraft-cli export <file> -o /tmp/check.png --node "<frame name>"` and inspect the image.
2. Live: MCP `screenshot`, or export from the saved file.
3. Structural check: `figcraft-cli info <file>` — node counts and image counts should match expectations.

Report honestly: which steps ran, what failed and how it was fixed, what the render shows.

## 7. Gotchas (each one verified the hard way)

1. **A frame without `layout` cannot contain layers.** Give input-like frames `layout: "horizontal"`, `crossAlign: "center"`, `padding: [0,16,0,16]` and let text flow in — do not absolutely position children.
2. **Ids continue from existing nodes**, not from 1. Count creation steps carefully; a wrong parent id fails late with a confusing error.
3. **Failed steps roll back the whole script** — nothing is written. Safe to iterate.
4. **`image.place` is raster-only** (`not a valid FigCraft file: unsupported image`). Rasterize SVGs first.
5. **The app never reloads from disk** (§3.3). Live mode exists precisely to avoid this.
6. **Port must match** between `--control` and `--attach`. If attach fails, check the app's launch flags (`pgrep -fl FigCraft`) and `lsof -nP -iTCP:<port> -sTCP:LISTEN`.
7. **New top-level frames auto-place** to the right of existing frames when created without x/y — convenient, but check `info`/`inspect_document` if exact placement matters.

## 8. Decision guide

| Situation | Do |
|---|---|
| App running with `--control` | Live mode via MCP; `save_file` after edits |
| App not running, quick file edit | Headless `run --in --out`; tell user to reopen (no save) if they had it open |
| Unknown command/param | `figcraft-cli commands --json` / MCP `list_commands` |
| Need to see the result | `export`/`render` + vision check, or MCP `screenshot` |
| Design from scratch | `template.list` → `file.newFromTemplate` → build on top |
