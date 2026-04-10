# arcrouter-mcp

MCP (Model Context Protocol) server for [ArcRouter](https://arcrouter.com) — use intelligent LLM routing directly from Claude Code, Cursor, Cline, and other MCP-compatible AI tools.

## Install

Add ArcRouter to your MCP client in one command:

```bash
claude mcp add arcrouter --transport http https://api.arcrouter.com/mcp
```

Or add to your MCP config manually:

```json
{
  "mcpServers": {
    "arcrouter": {
      "url": "https://api.arcrouter.com/mcp",
      "transport": "http"
    }
  }
}
```

## Tools

The server exposes 3 tools:

### `arcrouter_chat`

Route a prompt to the best model.

| Parameter | Type | Description |
|-----------|------|-------------|
| `prompt` | string | The message to send |
| `budget` | string | `free` \| `economy` \| `auto` \| `premium` |
| `mode` | string | `default` \| `council` |
| `session_id` | string | Session ID for model pinning |

**Returns:** Response text, model used, topic detected, cost estimate.

**Example:**
```
Use arcrouter to explain how transformers work, with budget=premium
```

### `arcrouter_models`

List models with benchmark scores and pricing.

| Parameter | Type | Description |
|-----------|------|-------------|
| `topic` | string | Filter by topic (e.g. `code`, `math`, `science`) |
| `budget` | string | Filter by budget tier |
| `limit` | number | Max results (default 10) |

**Returns:** Model list with quality scores, cost per 1M tokens, context length.

### `arcrouter_health`

Check ArcRouter system status.

**Returns:** Health status for database, routing, and all 5 direct providers (OpenAI, Anthropic, Google, DeepSeek, xAI).

## Authentication

The MCP server supports optional API key authentication:

- **Free tier:** No auth required. Uses free models only.
- **Paid tier:** Pass `Authorization: Bearer sk_...` header in your MCP config.

## What is ArcRouter?

ArcRouter routes LLM requests to the best model based on:
- **Topic detection** — code, math, science, writing, reasoning (24 categories)
- **Complexity scoring** — SIMPLE/MEDIUM/COMPLEX/REASONING tiers
- **Benchmark scores** — 345+ models scored on LiveBench, LiveCodeBench, HuggingFace
- **Cost optimization** — budget tiers from free to premium

## Links

- **API:** https://api.arcrouter.com
- **Docs:** https://arcrouter.com/docs
- **SDK:** `npm install arcrouter`
- **Classifier:** https://github.com/ArcRouterAI/arcrouter
