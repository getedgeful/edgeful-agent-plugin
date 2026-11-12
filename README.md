# edgeful agent plugin

connects your AI agent to the edgeful MCP server (`https://api.edgeful.com/mcp`). query the report catalog and discovery scans, build and save custom reports, and manage your account settings, report customizations and templates from your AI agent.

authentication is OAuth: on first use, your client opens an edgeful sign-in and consent screen. no API key is needed. tool calls require an edgeful account with an API-enabled plan (essential, pro or all access).

ships three manifests side by side: the portable [Agent Plugins](https://agent-plugins.org) format for any client that supports the standard, a [Cursor plugin](https://cursor.com/docs/reference/plugins) manifest that Cursor reads instead because it carries the logo, and a [Claude plugin](https://claude.com/docs/plugins/build) manifest read by claude.ai, the Claude desktop app, Cowork, and Claude Code. all three point at the same MCP server. keep their name, version, description and links the same when editing any of them.

## what this plugin does

the plugin contains no hooks, scripts or local code. it only declares the edgeful MCP server URL (`https://api.edgeful.com/mcp`). all requests go to that server after you sign in with edgeful, and the plugin sends no data anywhere else.

the client discovers tools from the server when it connects. new tools and fixes become available through this plugin once they are deployed to `https://api.edgeful.com/mcp`; they do not require a plugin release. if an existing session still shows an older tool list, reconnect the server or restart the client. changes available only on a preview API are not available through the production URL.

## available tools

| tool | purpose | requires write permission |
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

see [MCP setup](https://www.edgeful.com/docs/getting-started/set-up-mcp) for the current tool list and plan requirements.

## writes and account changes

writes are enabled by default. in the [API dashboard](https://www.edgeful.com/api-dashboard), **let Claude write to your account** controls custom-report and settings writes. **connected Claude** applies to OAuth connections, including this plugin; **this API key** applies to clients using that key. turning writes off keeps read tools available.

custom-report builds and revisions use your edgeful AI allowance. runs execute saved code without a model call. review the report's methodology and sample before asking your agent to save it. saved reports appear under **my custom reports** in edgeful AI, marked **built via API**. see [build and save a custom report](https://www.edgeful.com/docs/getting-started/build-custom-reports) for the workflow and limits.

reload the edgeful app after changing settings through your agent. an older open tab can overwrite bookmark or watchlist changes when it next saves. saved templates appear in their pickers after reload or the app's five-minute template refetch; select a template there to apply it. report customizations apply to `call_report_endpoint` only when the agent passes the query returned by the customization tools. see [manage your settings](https://www.edgeful.com/docs/getting-started/manage-settings) for validation rules and write budgets.

## install

### Claude

once published: open **Customize > Plugins** in claude.ai or the Claude desktop app and add **edgeful**. connect the edgeful server from the plugin's **Connectors** tab and sign in with your edgeful account. it's then also available in Cowork and Claude Code.

### Cursor

once published: search for **edgeful** in the Cursor marketplace and click **Add to Cursor**. when Cursor asks you to authenticate, sign in and click **Allow**.

## try it

- "what are the top reports for ES right now?"
- "how often does NQ fill its opening gap on Mondays over the last 6 months?"
- "build a custom report for ES and show me its methodology and a sample before saving it"
- "add ES and NQ to my futures watchlist"
- "show me my saved report templates"

## local development

### Claude

claude.ai and the desktop app: from the repo root, build a zip with

```bash
git archive -o edgeful.zip HEAD
```

then go to **Customize > Plugins > Add > Upload plugin** and select `edgeful.zip`. `git archive` only includes committed files, so `.git` and `.DS_Store` stay out of the zip. connect the server from the plugin's **Connectors** tab.

Claude Code: clone this repo, then start Claude Code with `claude --plugin-dir <path-to-clone>`.

run `/mcp` inside the session and confirm `plugin:edgeful:edgeful` is listed. the first tool call opens the edgeful sign-in and consent screen.

to check the manifest after editing, run `claude plugin validate <path-to-clone>`.

to test against a non-production API, change `url` in `.mcp.json`.

### Cursor

clone this repo and copy the clone to `~/.cursor/plugins/local/edgeful`. copy, don't symlink: Cursor rejects local plugins whose symlink target lives outside `~/.cursor/plugins/local` (visible in the "Cursor Plugins" output log as `loadUserLocalPlugin ... rejected`). re-copy after editing.

restart Cursor (or run **Developer: Reload Window**), open **Customize** and confirm the `edgeful` plugin and its MCP server are listed. Cursor flags the server as needing authentication; click through, sign in with your edgeful account, and click **Allow**.

to test against a non-production API, change `url` in `mcp.json`.

## files

- `plugin.json` - plugin manifest (Agent Plugins format)
- `.cursor-plugin/plugin.json` - Cursor plugin manifest, with the logo
- `mcp.json` - Cursor MCP server declaration
- `.claude-plugin/plugin.json` - Claude plugin manifest
- `.mcp.json` - Claude MCP server declaration
- `assets/` - plugin icon (512 x 512 PNG), used for the Claude directory listing and the Cursor marketplace
- `README.md` - this file
- `LICENSE` - MIT license text

## license

MIT
