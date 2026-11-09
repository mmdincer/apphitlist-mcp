# AppHitlist MCP

Connect Claude, Cursor and other MCP clients to [AppHitlist](https://apphitlist.com/?utm_source=github&utm_medium=repo&utm_campaign=apphitlist-mcp&utm_content=readme-intro), an App Store research tool for indie developers. Ask your assistant which new apps already earn money, which apps are climbing the daily charts, how long an app's Meta ads have been running, and what users complain about.

AppHitlist exposes a **remote MCP server**. There is nothing to install or run locally. This repository holds the setup guide, the tool reference and an agent skill. It is not the server source code. There is no REST API; MCP is the programmatic interface.

- Endpoint: `https://apphitlist.com/api/mcp` (streamable HTTP)
- Auth: API key as `Authorization: Bearer <key>` (the `x-api-key` header also works)
- Registry name: `com.apphitlist/mcp` ([`server.json`](server.json))

## Quick start

1. Create a free AppHitlist account and an API key on the [MCP page](https://apphitlist.com/dashboard/mcp?utm_source=github&utm_medium=repo&utm_campaign=apphitlist-mcp&utm_content=readme-key). A key starts with `ahl_` and is shown once. You can have up to 5 active keys.
2. Add the server to your client. Replace `YOUR_API_KEY` with your key.

**Claude Code**

```bash
claude mcp add --transport http apphitlist https://apphitlist.com/api/mcp \
  --header "Authorization: Bearer YOUR_API_KEY"
```

**Cursor** (`~/.cursor/mcp.json` or `.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "apphitlist": {
      "url": "https://apphitlist.com/api/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

Other clients (VS Code, Codex CLI, Claude Desktop) are in [docs/clients.md](docs/clients.md). Keep the key out of files you commit: use an environment variable where your client supports it.

3. Ask something: *"Find new winning apps in fitness."*

## Tools

| Tool | Use it for | Plan |
|---|---|---|
| `find_new_winning_apps` | Apps released in the last 6 months that already earn $10K or more a month | Free and Pro |
| `find_top_earning_apps` | Highest-earning apps, in one category or across all | Free and Pro |
| `find_chart_movers` | Daily chart risers and new entries, 36 App Store countries | Free and Pro |
| `search_apps` | Find an app or developer by name | Free and Pro |
| `get_app` | One app by App Store URL, id or name | Free and Pro |
| `find_longest_running_ads` | Active Meta ads that have run the longest | Pro |
| `get_app_ads` | Active Meta ads for one app | Pro |
| `get_category_complaints` | Common complaint topics from 1 and 2 star reviews in a category | Pro |
| `get_app_complaints` | Complaint themes for one app | Pro |

Parameters, defaults and limits are in [docs/tools.md](docs/tools.md).

## Free and Pro

| | Free account | Pro |
|---|---|---|
| Calls | 20 tool calls per month | No monthly cap, 60 calls per minute |
| Revenue and downloads | Ranges (`revenueRange`, `downloadsRange`) | One monthly figure (`latestRevenueUsd`, `latestDownloads`), not a range |
| Price and ratings | Not included | Included in search, top earners, chart movers and app details |
| Ads and complaint tools | Return `subscription_required` | Included |

Revenue and download figures come from AppHitlist's own model. See the [pricing page](https://apphitlist.com/pricing?utm_source=github&utm_medium=repo&utm_campaign=apphitlist-mcp&utm_content=readme-pricing) for current plans.

## Agent skill

[`skills/apphitlist/SKILL.md`](skills/apphitlist/SKILL.md) teaches an AI agent which tool to call for which question, how to chain them for niche discovery, competitor and ad research and complaint analysis, and how to read the output. Copy the `skills/apphitlist` folder into your agent's skills directory, for example `.claude/skills/` or `.cursor/skills/`.

## Install as a plugin

This repository is an [Agent Plugins](https://open-plugins.com/plugin-builders/specification) package: `plugin.json`, `mcp.json`, and the skill in `skills/apphitlist/`. Cursor Directory also reads [`.mcp.json`](.mcp.json). LobeHub reads [`lhm.plugin.json`](lhm.plugin.json).

The committed MCP entry is URL-only: `https://apphitlist.com/api/mcp`. It has no API key and no `Authorization` header. OAuth and API keys stay in the client, as in the quick start above.

```bash
npx skills add mmdincer/apphitlist-mcp
```

## Other

- Not the server code: the MCP server runs inside the AppHitlist app and its source is not published here.
- Issues with this guide: open an issue on this repository. Account or billing questions: use the contact page on [apphitlist.com](https://apphitlist.com/?utm_source=github&utm_medium=repo&utm_campaign=apphitlist-mcp&utm_content=readme-footer).
- License: [MIT](LICENSE) for the content of this repository. The AppHitlist service and its data are subject to the service terms on the site.
