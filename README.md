# MuleSoft Opportunities MCP System API - Local MCP Testing

This project exposes MCP tools over Streamable HTTP.

## MCP endpoints used locally

- MCP server endpoint: `http://localhost:8081/mcp`
- Inspector UI base: `http://127.0.0.1:6274`

The MCP endpoint is configured by:
- host/port: `http.host`, `http.port`
- path: `/mcp`

## Start the Mule app

Run the Mule application so port `8081` is listening.

Quick check:

```bash
lsof -nP -iTCP:8081 -sTCP:LISTEN
```

## Launch MCP Inspector

```bash
npx @modelcontextprotocol/inspector
```

Inspector prints a line like:

```text
MCP Inspector Web is up and running at:
http://127.0.0.1:6274?MCP_INSPECTOR_API_TOKEN=<long-token>
```

## How to get the complete Inspector URL

The complete URL is the **base UI URL plus query token** that Inspector prints at startup:

`http://127.0.0.1:6274?MCP_INSPECTOR_API_TOKEN=<token>`

Use the exact tokenized URL from that run. The token can change when Inspector restarts.

If you only open `http://localhost:6274` and it fails or asks auth, use the printed `127.0.0.1` URL with `MCP_INSPECTOR_API_TOKEN`.

## Manual server setup values (add new server)

When adding a server manually in MCP Inspector (or any MCP client), use:

- Server ID: `opportunities-local`
- Transport: `Streamable HTTP`
- URL: `http://localhost:8081/mcp`

Notes:

- Server ID is a local label. Any unique value is fine (for example, `mulesoft-opps-dev`).
- Keep URL path as `/mcp` to match this project's MCP server config.

## Connect Inspector to this MCP server

In Inspector:

- Transport: `Streamable HTTP`
- Server URL: `http://localhost:8081/mcp`

Then list tools and invoke:

- `list_opportunities`
- `update_opportunity`

## One-command validation

```bash
# Inspector must listen on 6274
lsof -nP -iTCP:6274 -sTCP:LISTEN

# Mule app must listen on 8081
lsof -nP -iTCP:8081 -sTCP:LISTEN
```

## Common local issues

1. `ERR_CONNECTION_REFUSED` on `6274`
- Inspector is not running yet.

2. Inspector install hangs on first run
- Ensure npm registry uses HTTPS:

```bash
npm config set registry https://registry.npmjs.org/
```

3. `localhost:6274` fails but `127.0.0.1:6274` works
- Use the exact URL printed by Inspector (`127.0.0.1` + token).

4. `MCP Inspector PORT IS IN USE` and startup does not print token
- This means Inspector is already running. Get the token from the active UI page source:

```bash
curl -s http://127.0.0.1:6274/ | grep -o 'window.__INSPECTOR_API_TOKEN__ = "[^"]*"'
```

- Then open:

```text
http://127.0.0.1:6274?MCP_INSPECTOR_API_TOKEN=<token>
```

- Or stop the running Inspector process and restart to print a fresh token:

```bash
lsof -nP -iTCP:6274 -sTCP:LISTEN
kill <PID>
npx @modelcontextprotocol/inspector
```
