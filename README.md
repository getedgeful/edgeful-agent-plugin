# edgeful agent plugin

Connects your AI agent to the edgeful MCP server (`https://api.edgeful.com/mcp`). Exposes the edgeful report catalog as tools: `list_allowed_report_endpoints`, `describe_report_endpoint`, `call_report_endpoint`, and `get_discovery_scan`.

Authentication is OAuth: on first use, your client opens an edgeful sign-in and consent screen. No API key is needed. Tool calls require an edgeful account with an API-enabled plan (Essential, Pro, or All Access).

Uses the portable [Agent Plugins](https://agent-plugins.org) format, so the same bundle works in Cursor and any other client that supports the standard.

## Install in Cursor

Once published: search for **edgeful** in the Cursor marketplace and click **Add to Cursor**.

### Local install (before publishing / for development)

```bash
git clone https://github.com/edgeful/edgeful-agent-plugin
cp -r edgeful-agent-plugin ~/.cursor/plugins/local/edgeful
```

Copy, don't symlink: Cursor rejects local plugins whose symlink target lives outside `~/.cursor/plugins/local` (visible in the "Cursor Plugins" output log as `loadUserLocalPlugin ... rejected`). Re-copy after editing.

Restart Cursor (or run **Developer: Reload Window**), open **Customize** and confirm the `edgeful` plugin and its MCP server are listed. Cursor flags the server as needing authentication; click through, sign in with your edgeful account, and click **Allow**.

To test against a non-production API, change `url` in `mcp.json`.

## Try it

- "what are the top reports for ES right now?"
- "how often does NQ fill its opening gap on Mondays over the last 6 months?"

## Files

- `plugin.json` - plugin manifest
- `mcp.json` - MCP server declaration

## License

MIT
