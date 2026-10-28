# edgeful agent plugin

Connects your AI agent to the edgeful MCP server (`https://api.edgeful.com/mcp`). Query the report catalog and discovery scans, build and save custom reports, and manage your account settings, report customizations and templates from your AI agent.

Authentication is OAuth: on first use, your client opens an edgeful sign-in and consent screen. No API key is needed. Tool calls require an edgeful account with an API-enabled plan (Essential, Pro, or All Access).

Ships two manifests side by side: the portable [Agent Plugins](https://agent-plugins.org) format for Cursor and any other client that supports the standard, and a [Claude plugin](https://claude.com/docs/plugins/build) manifest read by claude.ai, the Claude desktop app, Cowork, and Claude Code. Both point at the same MCP server.

## What this plugin does

The plugin contains no hooks, scripts or local code. It only declares the edgeful MCP server URL (`https://api.edgeful.com/mcp`). All requests go to that server after you sign in with edgeful, and the plugin sends no data anywhere else.

The client discovers tools from the server when it connects. New tools and fixes become available through this plugin once they are deployed to `https://api.edgeful.com/mcp`; they do not require a plugin release. If an existing session still shows an older tool list, reconnect the server or restart the client. Changes available only on a preview API are not available through the production URL.

## Available tools

| Tool | Purpose | Requires write permission |
| --- | --- | --- |
| `list_allowed_report_endpoints` | discover standard reports | no |
| `describe_report_endpoint` | read a standard report's parameters | no |
| `call_report_endpoint` | fetch a standard report | no |
| `get_discovery_scan` | find reports for a ticker | no |
| `build_custom_report` | build from a brief or return a clarifying question | yes |
| `revise_custom_report` | answer a clarification or revise the draft | yes |
| `run_custom_report` | run a draft or saved report with selected inputs | yes |
| `save_custom_report` | publish a reviewed draft to my custom reports | yes |
| `list_custom_reports` | list your saved reports | no |
| `get_custom_report_latest_result` | read the latest stored result without running | no |
| `delete_custom_report` | delete a saved report | yes |
| `get_account_settings` | read your defaults, sessions, bookmarks, watchlists and templates | no |
| `list_report_catalog` | list report pages for bookmarks, defaults and templates | no |
| `describe_report_customizations` | describe a report page's customizations and the query that applies them | no |
| `update_report_customizations` | set or reset report customizations by setting key | yes |
| `update_bookmarks` | add and remove report bookmarks | yes |
| `update_watchlist` | add and remove tickers on one market's watchlist | yes |
| `update_profile_defaults` | change your default market, ticker, session, date range or report | yes |
| `save_custom_session` | create or update a custom session | yes |
| `delete_custom_session` | delete a custom session | yes |
| `list_templates` | list your templates, with the built-in ones as read-only entries | no |
| `save_template` | create or update a report template | yes |
| `delete_template` | delete a template | yes |

See [MCP setup](https://docs.edgeful.com/getting-started/set-up-mcp) for the current tool list and plan requirements.

## Writes and account changes

Writes are enabled by default. In the [API dashboard](https://www.edgeful.com/api-dashboard), **let Claude write to your account** controls custom-report and settings writes. **Connected Claude** applies to OAuth connections, including this plugin; **this API key** applies to clients using that key. Turning writes off keeps read tools available.

Custom-report builds and revisions use your Edgeful AI allowance. Runs execute saved code without a model call. Review the report's methodology and sample before asking your agent to save it. Saved reports appear under **my custom reports** in Edgeful AI, marked **built via API**. See [build and save a custom report](https://docs.edgeful.com/getting-started/build-custom-reports) for the workflow and limits.

Reload the Edgeful app after changing settings through your agent. An older open tab can overwrite bookmark or watchlist changes when it next saves. Saved templates appear in their pickers after reload or the app's five-minute template refetch; select a template there to apply it. Report customizations apply to `call_report_endpoint` only when the agent passes the query returned by the customization tools. See [manage your settings](https://docs.edgeful.com/getting-started/manage-settings) for validation rules and write budgets.

## Install

### Claude

Once published: open **Customize > Plugins** in claude.ai or the Claude desktop app and add **edgeful**. Connect the edgeful server from the plugin's **Connectors** tab and sign in with your edgeful account. It's then also available in Cowork and Claude Code.

### Cursor

Once published: search for **edgeful** in the Cursor marketplace and click **Add to Cursor**. When Cursor asks you to authenticate, sign in and click **Allow**.

## Try it

- "what are the top reports for ES right now?"
- "how often does NQ fill its opening gap on Mondays over the last 6 months?"
- "build a custom report for ES and show me its methodology and a sample before saving it"
- "add ES and NQ to my futures watchlist"
- "show me my saved report templates"

## Local development

### Claude

claude.ai and the desktop app: from the repo root, build a zip with

```bash
git archive -o edgeful.zip HEAD
```

then go to **Customize > Plugins > Add > Upload plugin** and select `edgeful.zip`. `git archive` only includes committed files, so `.git` and `.DS_Store` stay out of the zip. Connect the server from the plugin's **Connectors** tab.

Claude Code:

```bash
git clone https://github.com/getedgeful/edgeful-agent-plugin
claude --plugin-dir ./edgeful-agent-plugin
```

Run `/mcp` inside the session and confirm `plugin:edgeful:edgeful` is listed. The first tool call opens the edgeful sign-in and consent screen.

To check the manifest after editing, run `claude plugin validate ./edgeful-agent-plugin`.

To test against a non-production API, change `url` in `.mcp.json`.

### Cursor

```bash
git clone https://github.com/getedgeful/edgeful-agent-plugin
cp -r edgeful-agent-plugin ~/.cursor/plugins/local/edgeful
```

Copy, don't symlink: Cursor rejects local plugins whose symlink target lives outside `~/.cursor/plugins/local` (visible in the "Cursor Plugins" output log as `loadUserLocalPlugin ... rejected`). Re-copy after editing.

Restart Cursor (or run **Developer: Reload Window**), open **Customize** and confirm the `edgeful` plugin and its MCP server are listed. Cursor flags the server as needing authentication; click through, sign in with your edgeful account, and click **Allow**.

To test against a non-production API, change `url` in `mcp.json`.

## Files

- `plugin.json` - Cursor plugin manifest (Agent Plugins format)
- `mcp.json` - Cursor MCP server declaration
- `.claude-plugin/plugin.json` - Claude plugin manifest
- `.mcp.json` - Claude MCP server declaration
- `README.md` - this file
- `LICENSE` - MIT license text

## License

MIT
