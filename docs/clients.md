# Client setup

Endpoint: `https://apphitlist.com/api/mcp` (streamable HTTP). The server takes an API key, not OAuth. Your client must be able to send a custom `Authorization: Bearer <key>` header, or an `x-api-key: <key>` header. Clients that only support OAuth or no-auth connectors cannot connect.

Create a key on the [MCP page](https://apphitlist.com/dashboard/mcp?utm_source=github&utm_medium=repo&utm_campaign=apphitlist-mcp&utm_content=clients) (free account). Every example uses the placeholder `YOUR_API_KEY`. Never commit a real key.

## Claude Code

```bash
claude mcp add --transport http apphitlist https://apphitlist.com/api/mcp \
  --header "Authorization: Bearer YOUR_API_KEY"
```

## Cursor

`~/.cursor/mcp.json` (all projects) or `.cursor/mcp.json` (one project):

```json
{
  "mcpServers": {
    "apphitlist": {
      "url": "https://apphitlist.com/api/mcp",
      "headers": { "Authorization": "Bearer ${env:APPHITLIST_API_KEY}" }
    }
  }
}
```

Set `APPHITLIST_API_KEY` in your shell environment, or put the key in the header directly if the file stays out of version control. Restart Cursor after changing environment variables.

## VS Code

`.vscode/mcp.json`. The `inputs` entry makes VS Code ask for the key once and store it, so the file has no secret:

```json
{
  "inputs": [
    { "type": "promptString", "id": "apphitlist-key", "description": "AppHitlist API key", "password": true }
  ],
  "servers": {
    "apphitlist": {
      "type": "http",
      "url": "https://apphitlist.com/api/mcp",
      "headers": { "Authorization": "Bearer ${input:apphitlist-key}" }
    }
  }
}
```

## Codex CLI

`~/.codex/config.toml`:

```toml
[mcp_servers.apphitlist]
url = "https://apphitlist.com/api/mcp"
bearer_token_env_var = "APPHITLIST_API_KEY"
```

Export `APPHITLIST_API_KEY` before starting Codex.

## Claude Desktop

Claude Desktop's config file starts local (stdio) servers, so bridge the remote server with [`mcp-remote`](https://www.npmjs.com/package/mcp-remote). In `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "apphitlist": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://apphitlist.com/api/mcp",
        "--header",
        "Authorization:${AUTH_HEADER}"
      ],
      "env": { "AUTH_HEADER": "Bearer YOUR_API_KEY" }
    }
  }
}
```

## Other clients

Any MCP client that supports streamable HTTP and custom headers works. Point it at the endpoint above and send `Authorization: Bearer YOUR_API_KEY`. Gateways that cannot set `Authorization` can send `x-api-key: YOUR_API_KEY`.

## Check it works

Ask: *"Find new winning apps in fitness."* The first call also confirms the key. A `401` means the key is missing, mistyped or revoked: create a new one.

## Limits

20 tool calls per month on a free account, 60 calls per minute on Pro. When a limit is hit the tool returns a message with the reset time instead of data.
