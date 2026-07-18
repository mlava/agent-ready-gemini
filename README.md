# Agent Ready — Gemini CLI extension / Antigravity plugin

Scan any URL for **AI agent-readability** from inside [Gemini CLI](https://geminicli.com) (and,
after migration, the Antigravity CLI). It checks sites against the **Vercel Agent Readability
Spec**, the **llmstxt.org** standard, and agent-protocol manifests (MCP server cards, A2A,
`agents.json`, `agent-permissions.json`, UCP, x402, NLWeb).

This extension is a thin wrapper around the existing
[`agent-ready-mcp`](https://www.npmjs.com/package/agent-ready-mcp) server and the
[`agent-ready` CLI](https://www.npmjs.com/package/agent-ready-scanner) — both published to npm and
fetched on demand via `npx`. There is nothing to build.

It gives Gemini two ways to use Agent Ready:

- **MCP tools** the agent can call autonomously: `scan_site`, `get_scan`, `ask`.
- **Slash commands** you invoke directly: `/agent-ready:scan`, `:get`, `:list`, `:ask`.

## Requirements

- [Gemini CLI](https://geminicli.com) installed.
- Node.js ≥ 20 (for the `npx`-launched MCP server and CLI).
- **No API key required to start** — `scan`/`scan_site` and `ask` work on the free
  anonymous tier (scan: 3 per 30 days per IP, 25-page depth). A Pro API key from
  <https://agent-ready.dev/dashboard/api-keys> unlocks `get`/`list` (scan history),
  deeper 250-page scans, and higher volume.

## Install

```bash
gemini extensions install https://github.com/mlava/agent-ready-gemini
```

Local development (symlink instead of clone):

```bash
gemini extensions link /path/to/agent-ready-gemini
```

Manage it with the usual commands:

```bash
gemini extensions list
gemini extensions disable agent-ready
gemini extensions uninstall agent-ready
```

## Configure the API key (optional)

Only needed for `get`/`list` and deeper scans — `scan` and `ask` work keyless.

```bash
gemini extensions config agent-ready
```

This stores `AGENT_READY_API_KEY` securely (system keychain) and passes it to the MCP server. The
optional `AGENT_READY_API_URL` overrides the API base URL (defaults to `https://agent-ready.dev`).

> The slash commands shell out to the `agent-ready` CLI, which reads `AGENT_READY_API_KEY` from the
> environment. If `gemini extensions config` does not propagate the key into shell-command
> execution in your version, also `export AGENT_READY_API_KEY=ar_live_...` in your shell. The
> `ask` command never needs a key.

## Usage

### Slash commands

```text
/agent-ready:scan https://example.com
/agent-ready:scan https://example.com --page-limit 25
/agent-ready:get V1StGXR8_Z
/agent-ready:list
/agent-ready:list --limit 5
/agent-ready:ask how is the score calculated?
/agent-ready:ask what does check S4 do? --type checks
```

Each command runs the corresponding `agent-ready` CLI subcommand and asks Gemini to summarize the
JSON output. Gemini will prompt for confirmation before running the shell command.

### Let the agent do it

Because the MCP server is registered, you can also just ask in natural language:

```text
Scan example.com for AI agent-readability and tell me what to fix first.
```

Gemini will call the `scan_site` tool itself, then `get_scan` if the scan is still running.

## Antigravity CLI

Gemini CLI is being replaced by the [Antigravity CLI](https://antigravity.google) (`agy`) for
individual-tier users (deadline **June 18, 2026**). Import this extension as a native Antigravity
plugin with:

```bash
agy plugin import gemini
```

This works cleanly here because the extension stays within the portable subset:

- The MCP server is **local stdio** (`command`/`args`), which carries over unchanged — the
  `url` → `serverUrl` rename only affects *remote* HTTP MCP servers, and this extension has none.
- `commands/*.toml` are collapsed into Antigravity **skills** automatically during import.
- `GEMINI.md` keeps working as the context file.
- No custom themes or Node-only API dependencies (the only things that don't migrate).

### Using it in Antigravity (after import)

Invocation differs from Gemini CLI — this is expected:

- There is **no `/agent-ready:scan` slash command** in `agy`. The commands become **skills**
  (named `scan`, `get`, `list`, `ask` under `~/.gemini/config/plugins/agent-ready/skills/`) that the
  agent invokes, and the **MCP tools** (`scan_site`, `get_scan`, `ask`) are called by the agent
  directly. Just ask in natural language, e.g. *"use agent-ready to scan https://example.com"* or
  *"ask agent-ready how the score is calculated"* (the latter is public, no key).
- **API key:** the imported `~/.gemini/config/plugins/agent-ready/mcp_config.json` has `env: null`.
  For keyed `scan`/`get`/`list`, add your key there
  (`"env": { "AGENT_READY_API_KEY": "ar_live_…" }`) for the MCP tools, and/or
  `export AGENT_READY_API_KEY=ar_live_…` in your shell for the skill shell-outs. `ask` needs no key.

## What's in here

```
gemini-extension.json     # manifest: MCP server + settings + contextFileName
GEMINI.md                 # context auto-loaded into the model
commands/agent-ready/     # /agent-ready:scan|get|list|ask (shell out to the CLI)
```

## Links

- Agent Ready: <https://agent-ready.dev>
- MCP server: <https://www.npmjs.com/package/agent-ready-mcp>
- CLI: <https://www.npmjs.com/package/agent-ready-scanner>

## License

MIT © Agent Ready
