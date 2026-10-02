# edgeful agent plugin

Connects your AI agent to the edgeful MCP server (`https://api.edgeful.com/mcp`). Exposes the edgeful report catalog as tools: `list_allowed_report_endpoints`, `describe_report_endpoint`, `call_report_endpoint`, and `get_discovery_scan`.

Authentication is OAuth: on first use, your client opens an edgeful sign-in and consent screen. No API key is needed. Tool calls require an edgeful account with an API-enabled plan (Essential, Pro, or All Access).

Ships two manifests side by side: the portable [Agent Plugins](https://agent-plugins.org) format for Cursor and any other client that supports the standard, and a [Claude plugin](https://claude.com/docs/plugins/build) manifest read by claude.ai, the Claude desktop app, Cowork, and Claude Code. Both point at the same MCP server.

## Install in Cursor

Once published: search for **edgeful** in the Cursor marketplace and click **Add to Cursor**.

### Local install (before publishing / for development)

```bash
git clone https://github.com/getedgeful/edgeful-agent-plugin
cp -r edgeful-agent-plugin ~/.cursor/plugins/local/edgeful
```

Copy, don't symlink: Cursor rejects local plugins whose symlink target lives outside `~/.cursor/plugins/local` (visible in the "Cursor Plugins" output log as `loadUserLocalPlugin ... rejected`). Re-copy after editing.

Restart Cursor (or run **Developer: Reload Window**), open **Customize** and confirm the `edgeful` plugin and its MCP server are listed. Cursor flags the server as needing authentication; click through, sign in with your edgeful account, and click **Allow**.

To test against a non-production API, change `url` in `mcp.json`.

## Install in Claude

Once published: open **Customize > Plugins** in claude.ai or the Claude desktop app and add **edgeful**. The plugin's **Connectors** tab lists the edgeful MCP server; connect it and sign in with your edgeful account. A plugin added there is also available in Cowork and in Claude Code.

### Local install (before publishing / for development)

claude.ai and the desktop app: zip the repo folder, then go to **Customize > Plugins > Add > Upload plugin** and select the zip. Connect the server from the plugin's **Connectors** tab.

Claude Code:

```bash
git clone https://github.com/getedgeful/edgeful-agent-plugin
claude --plugin-dir ./edgeful-agent-plugin
```

Run `/mcp` inside the session and confirm `plugin:edgeful:edgeful` is listed. The first tool call opens the edgeful sign-in and consent screen.

To check the manifest after editing, run `claude plugin validate ./edgeful-agent-plugin`.

## Try it

- "what are the top reports for ES right now?"
- "how often does NQ fill its opening gap on Mondays over the last 6 months?"

## Files

- `plugin.json` - Cursor plugin manifest (Agent Plugins format)
- `mcp.json` - Cursor MCP server declaration
- `.claude-plugin/plugin.json` - Claude plugin manifest
- `.mcp.json` - Claude MCP server declaration

## License

MIT
