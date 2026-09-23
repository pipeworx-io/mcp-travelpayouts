# mcp-travelpayouts

Travelpayouts MCP — wraps the Aviasales Flights Data API (travelpayouts.com)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1669+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `travelpayouts_cheap_prices` | Get the cheapest cached flight tickets for a route (one per destination, cheapest first). Uses IATA city/airport codes. Example: travelpayouts_cheap_prices({ origin: "MOW", destination: "BCN", currency: "usd", _apiKey: "token:marker" }) |
| `travelpayouts_price_calendar` | Get the cheapest ticket price for each day of a month for a route (a price calendar). Uses IATA city/airport codes. Example: travelpayouts_price_calendar({ origin: "MOW", destination: "BCN", depart_date: "2026-08", currency: "usd", _apiKey: "token:marker" }) |
| `travelpayouts_cheapest_by_month` | Get the cheapest ticket price for each day of a month grouped by number of transfers (month matrix). Uses IATA city/airport codes. Example: travelpayouts_cheapest_by_month({ origin: "LED", destination: "HKT", month: "2026-08-01", currency: "usd", _apiKey: "token:marker" }) |
| `travelpayouts_popular_destinations` | Get the most popular destinations (with cheapest cached prices) reachable from a city. Uses IATA city codes. Example: travelpayouts_popular_destinations({ origin: "MOW", currency: "usd", _apiKey: "token:marker" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "travelpayouts": {
      "url": "https://gateway.pipeworx.io/travelpayouts/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/travelpayouts/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1669+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/travelpayouts_cheap_prices`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "travelpayouts": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-travelpayouts"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-travelpayouts
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Travelpayouts data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
