<p align="center"><img src="assets/logo.svg" alt="DronaHQ" width="64" height="64"></p>

# DronaHQ MCP

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?name=dronahq&config=eyJ1cmwiOiJodHRwczovL3N0dWRpby5kcm9uYWhxLmNvbS9hcGkvdmliZS9tY3AifQ==)
[![Install in VS Code](https://img.shields.io/badge/VS_Code-Install_DronaHQ_MCP-0098FF?style=flat-square&logo=visualstudiocode&logoColor=white)](https://insiders.vscode.dev/redirect/mcp/install?name=dronahq&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A//studio.dronahq.com/api/vibe/mcp%22%7D)
[![Add to Claude](https://img.shields.io/badge/Claude-Add_DronaHQ_connector-D97757?style=flat-square&logo=claude&logoColor=white)](https://claude.ai/customize/connectors)

The Cursor and VS Code buttons install the DronaHQ cloud server (`studio.dronahq.com`) in one click. Claude has no install link yet, so **Add to Claude** opens your connectors page. There, choose **Add → Add custom connector**, name it `DronaHQ`, paste `https://studio.dronahq.com/api/vibe/mcp`, then click **Add** and **Connect** ([full steps](docs/clients.md#claude-web-and-desktop)). If you're self-hosted, see [Quickstart](#quickstart) and use your own Studio host.

Model Context Protocol (MCP) server that lets AI agents and coding assistants (Cursor, Claude, ChatGPT, VS Code, Codex, Gemini CLI, and any other MCP client) build and ship internal tools on top of your existing infrastructure. Connector credentials never enter the agent's context, and the apps run inside your DronaHQ workspace.

Studio hosts the server. There is no local package to install. You add a server URL, sign in with your DronaHQ account, and the agent calls tools on that workspace.

Full setup guide: [Getting started](https://docs.dronahq.com/building-dronahq-mcp/getting-started/).

## Why this exists

AI coding agents make generating UI and CRUD logic easy. Shipping those apps inside an enterprise breaks down on day two:

- **Secret sprawl.** Pasting connection strings or API keys into `.env` files or the model context fails security review.
- **Missing auth and access control.** Generated apps rarely implement SSO or catalogue and group permissions.
- **Shadow AI.** Untracked internal apps on external prototype hosts create compliance and maintenance liability.

DronaHQ MCP is the governed bridge. The agent receives schemas, saved operations, and app files. Credentials, sign-in, permissions, and hosting stay inside DronaHQ.

```
┌─────────────────────┐      MCP over HTTP (schemas, files,      ┌──────────────────────────┐
│  AI coding agent    │      queries — no raw credentials)       │   DronaHQ MCP            │
│ Any MCP client:     │ ◄──────────────────────────────────────► │   {studio}/api/vibe/mcp  │
│ IDE, CLI, or chat   │                                           └────────────┬─────────────┘
└─────────────────────┘                                                        │
                                                                  Query runtime uses the
                                                                  connector stored in Studio
                                                                  + SSO and app permissions
                                                                               │
                                                                  ┌────────────▼─────────────┐
                                                                  │  Your cloud or VPC       │
                                                                  │  Postgres, REST, internal│
                                                                  └──────────────────────────┘
```

## Prerequisites

- A DronaHQ workspace, cloud or self-hosted.
- A Studio role that can build: owner, admin, or creator. End users cannot use this MCP server.
- An MCP client that supports remote HTTP servers.

## Quickstart

Open Studio and go to **Connect DronaHQ MCP**. Copy the server URL, or use one of the one-click install buttons. The snippets contain no token.

The URL looks like one of these:

| URL | When to use it |
|---|---|
| `https://your-instance.dronahq.com/api/vibe/mcp` | Shared docs and onboarding. Each person picks their workspace when they sign in. If the workspace has no MCP server yet, allowing the connection creates one. |
| `https://your-instance.dronahq.com/api/vibe/mcp/{serverId}` | Pins one server. Shown in the Studio drawer. Signing in to another workspace asks you to switch. |

On first use the client receives a sign-in challenge, opens the browser, and you sign in the same way you sign in to Studio (password, Google, or SSO/SAML). Approve the client once. Later sessions refresh on their own. Connected clients appear in the Studio drawer, where you can disconnect one client without affecting the others.

For a self-hosted or VPC deployment, use that Studio host as the base URL. Tool calls then stay on your network.

### Pick your client

Works with any MCP client that supports remote HTTP servers: Cursor, VS Code and GitHub Copilot, Claude, Claude Code, ChatGPT, OpenAI Codex, Gemini CLI, Windsurf, Zed, JetBrains IDEs, Kiro, Amazon Q Developer, Cline, Continue, Goose, OpenCode, and others. Clients that only run local servers can use the [`mcp-remote` bridge](docs/clients.md#any-other-client).

Most clients only need the server name and URL:

```json
{
  "mcpServers": {
    "dronahq": {
      "url": "https://studio.dronahq.com/api/vibe/mcp"
    }
  }
}
```

Config file locations and field names vary by client. Step-by-step setup for each one is in [docs/clients.md](docs/clients.md), and copy-paste configs are in [examples/](examples/).

```bash
# Claude Code
claude mcp add --transport http dronahq -s user "https://studio.dronahq.com/api/vibe/mcp"

# OpenAI Codex (after adding the server to ~/.codex/config.toml)
codex mcp login dronahq
```

## What the agent can do

The server exposes 64 tools. Names are what `tools/list` returns, and parameters are on each tool's input schema. [docs/tools.md](docs/tools.md) lists every tool and marks which ones are read-only. Owners and admins can use every connector in the workspace. Creators only see connectors granted to them or their group, and they cannot add connectors or run raw queries that change data.

### Apps

| Tool | What it does |
|---|---|
| `vibe_list_apps` | List Vibe apps in the workspace. |
| `vibe_create_app` | Create a Vibe app and return its plugin id. |
| `vibe_get_app` | Read one app's metadata. |
| `vibe_list_files` | List source files. |
| `vibe_read_file` | Read one source file. |
| `vibe_write_file` | Create or replace one source file. |
| `vibe_delete_file` | Delete one source file. |
| `vibe_save_app` | Save source files so Studio preview shows them. |
| `vibe_preview_url` | Return the Studio preview URL. |
| `vibe_read_lowcode_source` | Read queries and a short screen list from an existing low-code app. |
| `vibe_publish` | Publish the app into a catalogue. |
| `vibe_check_in` | Save a version snapshot without publishing. |

### Connectors and data

The agent never receives connection strings. New connectors are added in Studio through a setup link (`magicUrl`). Secrets typed there stay in Studio.

| Tool | What it does |
|---|---|
| `vibe_list_connectors` | List connectors this account can use. |
| `vibe_get_connector_accounts` | List accounts on one connector. Non-secret fields only. |
| `vibe_get_db_schema` | Return tables and columns for one database connector. |
| `vibe_prepare_sql_query_instructions` | Return Data Agent context, live schema, and dialect notes before writing SQL. |
| `vibe_run_db_query` | Run one read-only SQL statement, or a batch of them. |
| `vibe_run_db_write` | Run one statement that changes data, or a raw NoSQL action. Owners and admins. |
| `vibe_list_subcats` | List saved queries and operations on a connector. |
| `vibe_get_subcat` | Read one saved operation, including its variables. |
| `vibe_get_subcat_input_schema` | Return the fields needed to save an operation on a connector. |
| `vibe_create_subcat` | Save a database query. |
| `vibe_create_http_read_subcat` | Save a GET or HEAD operation on an HTTP connector. |
| `vibe_create_http_write_subcat` | Save a POST, PUT, PATCH, or DELETE operation on an HTTP connector. |
| `vibe_run_subcat` | Run a saved operation. |
| `vibe_request_connector_setup` | Start a Studio setup link so an owner or admin can add a connector. |
| `vibe_get_connector_job` | Check whether that setup finished. |
| `vibe_bind_connectors` | Attach connectors to a Vibe app. |

Data Agent tools (`vibe_list_data_agents`, `vibe_get_data_agent_context`, `vibe_create_data_agent`, `vibe_update_data_agent`) curate which tables and views the agent should use on a SQL connector.

### Access

Access is catalogues, groups, and per-app permissions in the workspace. SSO is how people sign in. It is not a separate mapping tool.

| Tool | What it does |
|---|---|
| `vibe_list_catalogues` | List catalogues visible to this user. |
| `vibe_get_catalogue_users` | List groups and end users on one catalogue. |
| `vibe_create_catalogue` | Create a catalogue. Owners and admins. |
| `vibe_add_catalogue_groups` | Add groups to a catalogue. |
| `vibe_set_catalogue_groups` | Replace the groups on a catalogue. |
| `vibe_list_groups` | List end-user groups. |
| `vibe_check_users` | Check whether email addresses already exist and can be invited. Owners and admins. |
| `vibe_create_group` | Create an end-user group. |
| `vibe_add_user_to_group` | Invite a user or add them to groups. |
| `vibe_set_access` | Make a published app link public or private. |
| `vibe_get_app_permissions` | Read user and group permissions on one app. |
| `vibe_set_app_permissions` | Replace user and group permissions on one app. |

### Automations

Twenty `automation_*` tools create, edit, test, and publish automations in the same workspace: list, create, get, rename, duplicate, delete, set the trigger, add, update, move, and remove tasks, validate, test, publish, and turn on or off.

`automation_test_run` and `automation_test_task` skip tasks that change data. `automation_test_run_write` and `automation_test_task_write` include them. The full list is in [docs/tools.md](docs/tools.md#automations).

## Agent instructions

Both files give the agent the same guidance: never put secrets in source, read the schema before writing SQL, have app code call saved operations instead of raw SQL, and check in before publishing.

| File | Use it in |
|---|---|
| [rules/AGENTS.md](rules/AGENTS.md) | Paste into your project's `AGENTS.md` (Codex, GitHub Copilot, Cursor, Zed, Gemini CLI, and others), `CLAUDE.md` (Claude Code), `GEMINI.md`, or `.github/copilot-instructions.md`. |
| [rules/dronahq-vibe.mdc](rules/dronahq-vibe.mdc) | Copy into `.cursor/rules/`. |

## Security and governance

- **No credential exposure.** Connection strings and API secrets stay in the Studio connector store. They are used only when a query or saved operation runs. Connector setup strips secrets out of anything passed through MCP and sends the user to a Studio page to enter them.
- **Least privilege follows the signed-in user.** The token is that user's Studio session, not a shared workspace key. Creators only receive connectors an owner or admin has granted to them or their group. Adding a connector, managing groups, and raw queries that change data require an owner or admin.
- **Short-lived client access.** After sign-in the client holds an access token that lasts one hour and refreshes itself. Disconnect one client from the Studio drawer. **Rotate org secret** signs everyone out. Owners and admins can turn off pasted access tokens so only browser sign-in works.
- **Audit when a logger is configured.** Sign-in, disconnect, automatic server creation, org-secret rotation, and the access-token setting are written to the workspace audit logger. Query text is not copied into the model context.
- **VPC and self-hosted.** Point the client at your Studio URL. The MCP endpoint and the tool runtime are the same host, so payloads stay inside that deployment.

## Example prompts

Once the client shows as connected:

- "List the Postgres connectors I can use and build an internal refund-approval app for the Support catalogue."
- "Create a read-only customer lookup app on the crm_prod connector. Publish it to the Operations catalogue and keep the link private."
- "Read the orders schema, save a SELECT by customer id, and wire it into the app. Do not put the connection string anywhere in the source."

## Further reading

- [Getting started](https://docs.dronahq.com/building-dronahq-mcp/getting-started/)
- [Tool reference](docs/tools.md)
- [Connect a client](docs/clients.md)
- [Client config examples](examples/)

## Support

- Questions and bugs in these docs: [open an issue](https://github.com/dronahq/dronahq-mcp/issues).
- Workspace or account help: inquiries@dronahq.com
- Security reports: see [SECURITY.md](SECURITY.md).
- [Privacy policy](https://www.dronahq.com/privacy/)

## What's in this repository

This repository holds documentation and client configuration. The server runs inside DronaHQ Studio, so there is no server code to build or install here. [server.json](server.json) describes the hosted server for the [official MCP Registry](https://registry.modelcontextprotocol.io), and [.cursor-plugin/](.cursor-plugin/) holds the Cursor plugin manifest.

## License

[MIT](LICENSE)
