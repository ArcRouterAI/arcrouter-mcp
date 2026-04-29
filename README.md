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

The server exposes 6 tools:

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

### `arcrouter_council`

Run a prompt through council mode — multiple models answer in parallel and a chairman synthesizes verdicts. Higher-confidence answers for high-stakes questions; slower than `arcrouter_chat` (5-15s).

| Parameter | Type | Description |
|-----------|------|-------------|
| `prompt` | string | The question to verify |
| `budget` | string | Council member quality (`free`/`economy`/`auto`/`premium`) |

**Returns:** Synthesized answer + per-model votes + agreement percentage.

### `arcrouter_workflow`

Get usage stats for a workflow session — total spend, models used, latency, tier distribution.

| Parameter | Type | Description |
|-----------|------|-------------|
| `session_id` | string | Workflow session ID (from `workflow_budget.session_id`) |

**Returns:** Spend tracker + budget remaining + tier distribution. No auth required — `session_id` is the secret.

### `arcrouter_usage`

Get cumulative usage stats for an ArcRouter API key.

| Parameter | Type | Description |
|-----------|------|-------------|
| `api_key` | string | Your ArcRouter API key (`sk_...`) |
| `days` | number | Number of days to query (1-90, default 30) |

**Returns:** Daily request counts + total cost summary.

## Authentication

The MCP server supports four authentication tiers:

- **Free tier:** No auth required. Uses free models only (rate-limited 30 req/hr).
- **API key:** Pass `Authorization: Bearer sk_...` for paid tier (1000 req/hr).
- **x402 (USDC on Base):** Pass `X-PAYMENT` header for per-request crypto payment.
- **MPP (USDC.e on Tempo):** Pass `Authorization: Payment <credential>` header. Sub-cent fees, 500ms finality. See [mpp.dev](https://mpp.dev).

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
