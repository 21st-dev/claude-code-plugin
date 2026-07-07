# 21st — Claude Code plugin

One install wires up **both** the 21st MCP server and the 21st.dev Agent
Skills in Claude Code, so Claude searches, installs and publishes on 21st.dev
from the terminal or via MCP tool calls, instead of hand-writing UI.

## What's inside

- **MCP server `21st`** — the remote endpoint `https://21st.dev/api/mcp`
  (JSON-RPC over HTTP), exposing 21 tools: `search`, `get_component`,
  `get_theme`, bookmarks + bookmark lists, teams, `get_usage`, `generate`, and
  edit/delete for components/themes/templates. Metadata search is free;
  component code, generation and writes are metered.
- **Skills** — four focused Agent Skills that teach the `21st` CLI
  (`npx @21st-dev/cli`), auto-activating when the project has a
  `components.json`: `21st-cli-use` (search / install), `21st-ai` (generate,
  iterate on, and grab code from 21st AI drafts), `21st-registry` (publish &
  manage components/themes/templates), and `21st-design-sync` (publish the
  project's design tokens as a theme).

## Install

1. Set your 21st API key in the environment (get one at
   https://21st.dev/settings/api-keys):

   ```bash
   export API_KEY_21ST="sk_..."
   ```

2. Add this marketplace and install the plugin from inside Claude Code:

   ```
   /plugin marketplace add 21st-dev/claude-code-plugin
   /plugin install 21st@21st
   ```

   (`21st@21st` = plugin `21st` from marketplace `21st`. Replace
   `21st-dev/claude-code-plugin` with the actual `<org>/<repo>` that hosts this
   directory, or a local path / git URL.)

3. Restart Claude Code (or reload) so the MCP server connects. Verify with
   `/mcp` — you should see the `21st` server with its full tool list (`search`,
   `get_component`, `get_theme`, bookmarks, teams, `generate`, …).

## Auth

The MCP server reads your key from the `API_KEY_21ST` environment variable and
sends it as the `x-api-key` header (see `plugins/21st/.mcp.json`). If tool calls
report "Not authenticated," confirm `API_KEY_21ST` is exported in the shell that
launched Claude Code, then reconnect via `/mcp`.

## Layout

```
.claude-plugin/
  marketplace.json          # marketplace listing this plugin
plugins/
  21st/
    .claude-plugin/
      plugin.json           # skills + mcpServers (via ${CLAUDE_PLUGIN_ROOT})
    .mcp.json               # remote 21st MCP (x-api-key: ${API_KEY_21ST})
    skills/
      21st-cli-use/
        SKILL.md            # bundled Agent Skills (same source as
      21st-ai/              # https://21st.dev/api/skills/<name>)
        SKILL.md
      21st-registry/
        SKILL.md
      21st-design-sync/
        SKILL.md
```

Bundled paths in `plugin.json` use `${CLAUDE_PLUGIN_ROOT}` so they resolve
regardless of where the plugin is installed.
