# QualiTaTi for AI agents

Run **AI-moderated interviews, conversational surveys and qualitative analysis** on [QualiTaTi](https://qualitati.com) from Claude Code, Codex, OpenCode, Cursor, Claude Desktop or any MCP client.

One hosted [MCP](https://modelcontextprotocol.io) server does the work; this repo packages it as an installable plugin for the tools that have plugin systems and documents the one-line config for the rest. The plugin adds three skills that teach the agent QualiTaTi's workflows (setting up an interview study, building a survey, reading results) and two slash commands.

## Prerequisites

1. A QualiTaTi account — free tier is fine.
2. A personal API key: **qualitati.com → Profile → API keys** (keys start with `qt_`). The key inherits your plan, quotas and billing; revoke it there any time.
3. Put it in your shell: `export QUALITATI_API_KEY=qt_…`

Clients that implement MCP authorization (OAuth 2.1 with dynamic client registration) can skip the key entirely: point them at the server URL and a QualiTaTi consent page opens in your browser. Claude Code: `claude mcp add --transport http qualitati https://api.qualitati.com/mcp`, then `/mcp` → Authenticate.

## Install

### Claude Code

```
/plugin marketplace add lanceyuu/qualitati-agent-plugins
/plugin install qualitati@qualitati-agent-plugins
```

You get the `qualitati` MCP server (reads `QUALITATI_API_KEY`), the skills, and `/qualitati:new-study` and `/qualitati:results`. Without the plugin, the server alone:

```
claude mcp add --transport http qualitati https://api.qualitati.com/mcp \
  --header "Authorization: Bearer $QUALITATI_API_KEY"
```

### Codex CLI / app

```
codex plugin marketplace add lanceyuu/qualitati-agent-plugins
codex plugin add qualitati@qualitati-agent-plugins
```

Or configure the server directly in `~/.codex/config.toml`:

```toml
[mcp_servers.qualitati]
url = "https://api.qualitati.com/mcp"
bearer_token_env_var = "QUALITATI_API_KEY"
```

### OpenCode

```json
{
  "mcp": {
    "qualitati": {
      "type": "remote",
      "url": "https://api.qualitati.com/mcp",
      "enabled": true,
      "headers": { "Authorization": "Bearer {env:QUALITATI_API_KEY}" }
    }
  }
}
```

### Cursor

`.cursor/mcp.json` (or use the **Add to Cursor** button on https://qualitati.com/developers):

```json
{
  "mcpServers": {
    "qualitati": {
      "url": "https://api.qualitati.com/mcp",
      "headers": { "Authorization": "Bearer qt_your_key_here" }
    }
  }
}
```

### Claude Desktop

Custom connectors cannot send a fixed header yet, so bridge with `mcp-remote` in `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "qualitati": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://api.qualitati.com/mcp",
               "--header", "Authorization: Bearer qt_your_key_here"]
    }
  }
}
```

### MCP Registry

Listed as `io.github.lanceyuu/qualitati` on the official [MCP Registry](https://registry.modelcontextprotocol.io) (`server.json` in this repo), so registry-aware clients and directories find it without any of the above.

## What the agent can do

| Tool | What it does |
|---|---|
| `project_list`, `project_get`, `project_create`, `project_share` | find or set up an interview study and get the participant link |
| `project_interviews`, `interview_get`, `interview_import` | see completion status, read full transcripts, import external transcripts |
| `analysis_run`, `analysis_results`, `analysis_summary`, `analysis_digest` | run and read quality + coding analysis, digests, counts |
| `survey_create`, `survey_add_question`, `survey_publish` | build a conversational survey and publish it |

A human participant still conducts the interview via the share link; the agent sets studies up and reads the results.

## Repo layout

One plugin, two package layouts, shared skills:

```
.claude-plugin/marketplace.json     Claude Code marketplace (this repo, plugin source "./")
.claude-plugin/plugin.json          Claude Code plugin manifest
.agents/plugins/marketplace.json    Codex marketplace (this repo, plugin path "./")
plugin.json  mcp.json               Codex / Agent Plugins (Open Plugins) manifest + MCP config
.mcp.json                           Claude Code MCP config
skills/  commands/  assets/         shared
server.json                         MCP Registry entry
```
The repo root *is* the plugin (the Open Plugins layout cursor.directory scans), and the two marketplace files let Claude Code and Codex install it from the same repo.

## License

MIT. QualiTaTi is a product of QualiTaTi (contact@qualitati.com).
