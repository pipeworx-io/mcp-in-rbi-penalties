# @pipeworx/in-rbi-penalties

Has the Reserve Bank of India penalised this bank, non-bank lender, payment company or credit-information company? Enforcement press releases about firms, with the release text.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2007+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `in_rbi_penalties_list(name?, limit?)` — the bank's current enforcement press-release list, newest first: release date, the firm named in the title and a `prid`. `name` keeps rows whose title contains it. Returns `result` (`rows`, `no_title_match_in_current_window`, `no_enforcement_release_in_current_window`), `releases_scanned`, `non_firm_dropped`, `oldest_release_date`, `coverage` and `data_as_of`.
- `in_rbi_penalties_read(prid)` — one release as plain text: the order date, the penalty amount, the firm, the provisions of Indian law it was imposed under and the charges sustained. The signing officer's name line is removed and counted. A `prid` that is not a firm enforcement release returns `found: false` with a reason.

## Auth

Keyless.

## Data sources

- Reserve Bank of India, enforcement press releases, read from the publisher on request. `data_as_of` is the day of the read; release dates are the bank's own.

## Behaviour worth knowing

- Firms only. A list row whose title names no firm is dropped and counted in `non_firm_dropped`.
- The list covers the bank's current window, about the last three months of enforcement releases. Older releases are kept by the bank in a month-by-month archive that needs a form submission; they are not read, and every response says so. No row is not a clearance of a firm.
- A page that cannot be read or has changed shape is an error, never "no penalty found".

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "in-rbi-penalties": {
      "url": "https://gateway.pipeworx.io/in-rbi-penalties/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/in-rbi-penalties/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 2007+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/in_rbi_penalties_list \
  -H 'Content-Type: application/json' \
  -d '{"limit":5}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/in_rbi_penalties_list`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-in-rbi-penalties` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/in-rbi-penalties/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "in-rbi-penalties": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-in-rbi-penalties"]
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
ask_pipeworx({ question: "your question about In Rbi Penalties data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
