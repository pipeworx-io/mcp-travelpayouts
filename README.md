# mcp-travelpayouts

Travelpayouts MCP — wraps the Aviasales Flights Data API (travelpayouts.com)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1857+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

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

Both URLs reach the same gateway and the same 1857+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/travelpayouts_cheap_prices`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-travelpayouts` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/travelpayouts/mcp` endpoint shown above. It needs network access;
there is no offline mode.

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

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

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
