# AGENTS.md — Agent Ready Gemini extension

This repository is a **Gemini CLI extension / Antigravity plugin** that wires
[Agent Ready](https://agent-ready.dev) into Gemini CLI. It is a thin,
config-only wrapper around the published [`agent-ready-mcp`](https://www.npmjs.com/package/agent-ready-mcp)
server and the [`agent-ready` CLI](https://www.npmjs.com/package/agent-ready-scanner)
(both fetched on demand via `npx`) — there is nothing to build. `GEMINI.md` is
the Gemini-specific config; this file is the tool-agnostic summary.

## What Agent Ready does

Agent Ready scans any public URL for **AI agent-readability** — how well a site
exposes itself to LLMs, AI search engines, and autonomous agents — against the
**Vercel Agent Readability Spec**, the **llmstxt.org** standard
(`llms.txt` / `llms-full.txt`), and agent-protocol manifests (MCP server cards,
A2A agent cards, `agents.json`, `agent-permissions.json`, UCP, x402, NLWeb, plus
API Catalog, Web Bot Auth, Agent Skills Discovery, and agent-driven UI / A2UI).
A scan returns a Vercel readability score (0–100 + rating), an llms.txt score
(0–100), and a separate accessibility score (0–100, or null when no accessibility
checks ran; WCAG 2.2 / layout stability), with a per-check `howToFix` for every
failing check. It does **not** edit the target site.

## What's in this repo

- `gemini-extension.json` — the extension manifest (MCP server reference).
- `GEMINI.md` — the Gemini context file describing tools, commands, and usage.
- `commands/` — `/agent-ready:*` slash commands that shell out to the CLI.

## Tools and commands

- `ask` — natural-language search over Agent Ready's methodology, check
  registry, and validated specs. **Public; no API key.** Cheapest path for
  questions *about* Agent Ready.
- `scan_site` — scan a live URL; returns scores + per-check findings. **Works
  without a key** (anonymous free tier, 25-page cap); a Pro key unlocks 250-page
  scans and higher volume.
- `get_scan` — fetch a previous scan by id. **Needs a Pro API key** (scan history
  is account-scoped; keyless `scan_site` returns its full result inline).
- Slash commands (user-invoked): `/agent-ready:scan <url>`, `:get <id>`,
  `:list`, `:ask <question>`.

## Install

```bash
gemini extensions install https://github.com/mlava/agent-ready-gemini
```

(Antigravity: `agy plugin import gemini`.) Requires Gemini CLI and Node.js ≥ 20.

## Configuration

`scan_site` and the `scan` command work with no key on the anonymous free tier.
`get_scan` and the `get`/`list` commands require a Pro API key from
<https://agent-ready.dev/dashboard/api-keys> (a Pro key also deepens scans to
250 pages). Set it with `gemini extensions config agent-ready` (stored as
`AGENT_READY_API_KEY`). The `ask` tool/command is public and needs no key.

## Usage

Ask Gemini to "scan example.com for agent readiness", or run
`/agent-ready:scan https://example.com`. For methodology questions, use the
public `ask` tool/command. Canonical docs: <https://agent-ready.dev/docs>.
