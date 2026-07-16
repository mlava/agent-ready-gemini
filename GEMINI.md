# Agent Ready

Agent Ready scans any URL for **AI agent-readability** — how well a site exposes itself to LLMs,
AI search engines, and autonomous agents. It checks a page/site against:

- the **Vercel Agent Readability Spec**,
- the **llmstxt.org** standard (`llms.txt` / `llms-full.txt`), and
- agent-protocol manifests (MCP server cards, A2A, `agents.json`, `agent-permissions.json`, UCP,
  x402, NLWeb).

A scan returns three scores — a **Vercel readability score** (0–100, with a rating), an
**llms.txt score** (0–100), and a separate **accessibility score** (0–100, or null when no
accessibility checks ran; WCAG 2.2 / layout stability) — plus per-check findings with remediation
hints.

## Capabilities exposed by this extension

**MCP tools (the agent can call these autonomously):**
- `scan_site` — scan a URL; returns scores + per-check findings. Requires an API key.
- `get_scan` — fetch a scan by id (e.g. to poll one that was still running). Requires an API key.
- `ask` — natural-language search over Agent Ready's methodology, check registry, and supported
  specs. **Public — no API key.**

**Slash commands (user-invoked; they shell out to the `agent-ready` CLI):**
- `/agent-ready:scan <url>` — scan and summarize.
- `/agent-ready:get <id>` — fetch a prior scan.
- `/agent-ready:list` — list recent scans (`--limit`, `--cursor`).
- `/agent-ready:ask <question>` — ask the docs (public).

## When to use what

- Use **`ask`** for questions *about* Agent Ready — "how is the score calculated?", "what does
  check S4 do?", "what specs are validated?". It needs no key and is the cheapest path.
- Use **`scan_site`** (or `/agent-ready:scan`) to audit a **live URL**. This needs a Pro API key.
- Use **`get_scan`** to re-check a scan id, especially if a scan was queued and not yet finished.

## API key

`scan_site` / `get_scan` (and the `scan`/`get`/`list` slash commands) require a Pro API key from
<https://agent-ready.dev/dashboard/api-keys>. Set it with `gemini extensions config agent-ready`
(stored securely as `AGENT_READY_API_KEY`). The `ask` tool/command is public and needs no key.
