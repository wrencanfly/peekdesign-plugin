# Peekdesign — your agent's art director

Peekdesign connects your coding agent to a curated library of motion, branding and interface work from artists on X, and to the tools our own design agent uses. Your agent can:

- **turn a vibe into a style** — three directions for any search, each with a palette with roles, a Google Fonts pairing, how to build it, its references, CSS tokens and a ready `STYLE.md`,
- **find and study references** — plain-words search over the whole library, full media, and specs (palette, type, grid, a timed motion breakdown with easings),
- **follow our design guides** — the rules Peek designs by: websites and landing pages, product UI, motion, real glass and 3D in WebGL,
- **design on a live canvas** you can watch, with real-browser screenshots so it checks its own work (WebGL included; three.js, React Three Fiber, drei and GSAP import by name),
- **critique** a design against references (0–100 across six axes, with concrete fixes),
- **hand a brief to Peek**, our design agent, and bring the result back into your codebase.

Your agent does the heavy lifting on your own plan — thinking and writing code are free. Credits (included monthly with every paid plan, or as top-ups) pay only for what runs on our side: opening a design 1 · spec 3 · inspiration 5 · style directions 10 (the first 10 are free) · critique 10 · a frame change 1 · a screenshot 2 · Peek by its real model use. Searching, reading and guides are free. New accounts get 100 credits.

The MCP endpoint is `https://peekdesign.space/api/mcp` (locally: `http://localhost:3000/api/mcp`).

## Claude Code — plugin (recommended)

The plugin bundles the MCP server, four skills (`art-director`, `design-artifacts`, `design-critique`, `motion-direction`) and four commands:

| Command | What it does |
| --- | --- |
| `/peekdesign:style <search>` | Three style directions; saves the one you pick as `STYLE.md` and tokens |
| `/peekdesign:inspire <brief>` | Art direction for what you're building, from real references |
| `/peekdesign:peek <brief>` | Hands the brief to Peek, watches it on a canvas, brings the code back |
| `/peekdesign:critique [target]` | Scores a design and turns the critique into fixes |

Install it from inside Claude Code:

```
/plugin marketplace add wrencanfly/peekdesign-plugin
/plugin install peekdesign@peekdesign
```

Then run `/mcp`, select **peekdesign** and **Authenticate**. Your browser opens; sign in and click **Allow**. That's it — no keys to copy. Try: *"Call get_account and tell me my plan and credits."*

The plugin points at `https://peekdesign.space/api/mcp`. To use another instance (a local one while developing), export its URL before starting Claude Code:

```bash
export PEEKDESIGN_URL=http://localhost:3000
```

### Using an API key instead of OAuth

For CI, headless machines or if you prefer keys: create one at **Account → Agent access** (`/account#agents`), then add a `headers` block to `plugin/.mcp.json` (or your own project's `.mcp.json`):

```json
{
  "mcpServers": {
    "peekdesign": {
      "type": "http",
      "url": "${PEEKDESIGN_URL:-https://peekdesign.space}/api/mcp",
      "headers": { "Authorization": "Bearer ${PEEKDESIGN_API_KEY}" }
    }
  }
}
```

```bash
export PEEKDESIGN_API_KEY=df_live_…
```

## Claude Code — MCP only

No plugin, just the tools (MCP prompts `art-direct` and `critique` carry the workflow):

```bash
claude mcp add --transport http peekdesign https://<host>/api/mcp
# then /mcp → peekdesign → Authenticate

# or with an API key:
claude mcp add --transport http peekdesign https://<host>/api/mcp \
  --header "Authorization: Bearer $PEEKDESIGN_API_KEY"
```

## Cursor

Add to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (project):

```json
{
  "mcpServers": {
    "peekdesign": {
      "url": "https://<host>/api/mcp",
      "headers": { "Authorization": "Bearer ${env:PEEKDESIGN_API_KEY}" }
    }
  }
}
```

Set `PEEKDESIGN_API_KEY` in the environment Cursor is launched from. Cursor also supports OAuth for remote servers: omit `headers` and click **Connect** in Settings → MCP.

## VS Code (Copilot agent mode)

`.vscode/mcp.json`:

```json
{
  "servers": {
    "peekdesign": { "type": "http", "url": "https://<host>/api/mcp" }
  }
}
```

VS Code discovers OAuth automatically and prompts you to sign in.

## Claude.ai and Claude Desktop

1. **Settings → Connectors → Add custom connector.**
2. Name it *Peekdesign* and paste `https://<host>/api/mcp` as the URL.
3. Click **Connect** — sign in with your email code and **Allow**.

Enable it per chat from the tools menu. (Custom connectors need a publicly reachable HTTPS URL, so this works with a deployed instance, not `localhost`.)

## ChatGPT

1. **Settings → Apps & Connectors → Advanced settings → enable Developer mode.**
2. **Create connector**: name *Peekdesign*, MCP server URL `https://<host>/api/mcp`, authentication **OAuth**.
3. Sign in with your email code and **Allow**, then pick the connector in a chat's tools menu.

## Managing access

- **Account → Agent access** (`/account#agents`): create, view (prefix and last use) and revoke API keys. A key is shown once.
- **Account → Connected apps** (`/account#apps`): see and disconnect OAuth apps (Claude.ai, ChatGPT, Claude Code…).
- **Account → Credits** (`/account#credits`): balance, packs, prices and history.

## Tools

| Tool | Credits |
| --- | --- |
| `search_designs`, `list_guides`, `read_guide`, `list_components`, `list_websites`, `get_account`, `list_boards`, `read_board`, `read_frame`, `get_artifact`, `list_artifacts`, `get_peek_run` | free |
| `get_component` / `get_website` | free with Pro, Max or Ultra (Pro takes 15 Websites a month) |
| `get_style_directions` | 10 (first 10 free; a search anyone ran this week is free) |
| `get_design` / `get_design_spec` | 1 per day / 3 per month, per design |
| `find_inspiration` / `critique_design` | 5 / 10 |
| `create_board` · frame changes · `get_frame_screenshot` | 2 · 1 each (+2 with `look: true`) · 2 |
| `ask_peek` | Peek's real model use plus its tools |
| `create_artifact` / `update_artifact` | 2 / 1 |
| `generate_images` · `generate_design` | by model · 40 |

## What the plugin sends, and where

The plugin contains no code that runs on your machine: four skills and four commands (plain instructions for your agent) and one MCP server entry pointing at `https://peekdesign.space/api/mcp` (or `$PEEKDESIGN_URL`). Nothing reaches Peekdesign except the tool calls your agent makes, and each one carries only its own arguments:

- **Searches and briefs** — the words your agent searches with or describes the design in.
- **Code it chooses to send** — HTML/React for canvas frames and artifacts it writes, or a component's source passed to `critique_design`. Your agent never uploads your codebase on its own; it sends what a tool call needs.
- **Sign-in** — OAuth with your Peekdesign account (or an API key you create), so results land in your account.

What we keep: your canvases, frames, artifacts, generated images, search overviews and a log of tool calls and credits, on your account, until you delete them or ask us to. Our AI tools (style directions, critiques, Peek) send the relevant content to Anthropic to generate the result; image generation goes to the image model you pick. We don't sell personal data. Privacy policy: https://peekdesign.space/privacy · Terms: https://peekdesign.space/terms

## License

MIT for this plugin (the skills, commands and manifests). The Peekdesign service, its library and the artists' work are not covered by it.

## Ethics

Every reference credits its artist. The skills instruct your agent to credit them too and to borrow technique — grids, pacing, easing, palette relationships — never content such as copy, logos, characters or footage.
