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
- `scan_site` — scan a URL; returns scores + per-check findings. Works without a key on the
  anonymous free tier (25-page cap); a Pro key deepens scans to 250 pages.
- `get_scan` — fetch a scan by id. Requires a Pro API key (scan history is account-scoped;
  keyless `scan_site` returns its full result inline, so there's usually nothing to fetch).
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
- Use **`scan_site`** (or `/agent-ready:scan`) to audit a **live URL**. Works keyless on the
  anonymous free tier; a Pro key deepens the scan and raises limits.
- Use **`get_scan`** to re-check a scan id (Pro key required), especially if a scan was queued
  and not yet finished.

## API key

`scan_site` and the `scan` slash command work with no key on the anonymous free tier (3 scans per
30 days per IP, 25-page depth). `get_scan` and the `get`/`list` slash commands require a Pro API key
from <https://agent-ready.dev/dashboard/api-keys> (which also deepens scans to 250 pages). Set it
with `gemini extensions config agent-ready` (stored securely as `AGENT_READY_API_KEY`). The `ask`
tool/command is public and needs no key.
