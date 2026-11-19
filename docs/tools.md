# Tool reference

DronaHQ MCP exposes 64 tools. Names match what `tools/list` returns. Each tool's input schema is served by the server, so this page lists only what each tool is for.

**Read-only** tools carry `readOnlyHint: true`. Every other tool carries `destructiveHint: true`, so clients that ask before writes (Cursor, Claude, VS Code) will prompt for them.

What a tool can reach follows the signed-in user's Studio role. Owners and admins can use every connector in the workspace. Creators see only connectors granted to them or their group, cannot add connectors, and cannot run raw queries that change data.

## Apps

| Tool | Purpose | Read-only | Notes |
|---|---|:---:|---|
| `vibe_list_apps` | List Vibe apps | Yes |  |
| `vibe_create_app` | Create Vibe app |  |  |
| `vibe_get_app` | Get Vibe app | Yes |  |
| `vibe_read_lowcode_source` | Read low-code app source | Yes |  |
| `vibe_list_files` | List app files | Yes |  |
| `vibe_read_file` | Read app file | Yes |  |
| `vibe_write_file` | Write app file |  |  |
| `vibe_delete_file` | Delete app file |  |  |
| `vibe_save_app` | Save Vibe app |  |  |
| `vibe_preview_url` | Get preview URL | Yes |  |
| `vibe_check_in` | Check in app version |  |  |
| `vibe_publish` | Publish Vibe app |  |  |

## Connectors and data

| Tool | Purpose | Read-only | Notes |
|---|---|:---:|---|
| `vibe_list_connectors` | List connectors | Yes |  |
| `vibe_get_connector_accounts` | List connector accounts | Yes | Non-secret fields only. |
| `vibe_get_db_schema` | Get database schema | Yes |  |
| `vibe_prepare_sql_query_instructions` | Prepare SQL context | Yes |  |
| `vibe_run_db_query` | Run read-only database query | Yes | Rejects statements that change data. |
| `vibe_run_db_write` | Run database write |  | Owners and admins only. |
| `vibe_request_connector_setup` | Start connector setup |  | Returns a Studio link. An owner or admin enters the secrets there. |
| `vibe_get_connector_job` | Get connector setup status | Yes |  |
| `vibe_bind_connectors` | Bind connectors to app |  |  |

## Saved operations

| Tool | Purpose | Read-only | Notes |
|---|---|:---:|---|
| `vibe_list_subcats` | List saved operations | Yes |  |
| `vibe_get_subcat` | Get saved operation | Yes |  |
| `vibe_get_subcat_input_schema` | Get operation input schema | Yes |  |
| `vibe_create_subcat` | Create database query |  |  |
| `vibe_create_http_read_subcat` | Create HTTP read operation |  |  |
| `vibe_create_http_write_subcat` | Create HTTP write operation |  |  |
| `vibe_run_subcat` | Run saved operation |  | Not read-only, because a saved operation can change data. |

## Data Agents

| Tool | Purpose | Read-only | Notes |
|---|---|:---:|---|
| `vibe_list_data_agents` | List Data Agents | Yes |  |
| `vibe_get_data_agent_context` | Get Data Agent context | Yes |  |
| `vibe_create_data_agent` | Create Data Agent |  |  |
| `vibe_update_data_agent` | Update Data Agent |  |  |

## Access

| Tool | Purpose | Read-only | Notes |
|---|---|:---:|---|
| `vibe_list_catalogues` | List catalogues | Yes |  |
| `vibe_get_catalogue_users` | List catalogue members | Yes |  |
| `vibe_create_catalogue` | Create catalogue |  | Workspace owner or super admin only. |
| `vibe_add_catalogue_groups` | Add catalogue groups |  |  |
| `vibe_set_catalogue_groups` | Replace catalogue groups |  |  |
| `vibe_list_groups` | List groups | Yes |  |
| `vibe_check_users` | Check user emails | Yes | Owners and admins only. |
| `vibe_create_group` | Create group |  |  |
| `vibe_add_user_to_group` | Add user to groups |  |  |
| `vibe_set_access` | Set app access |  |  |
| `vibe_get_app_permissions` | Get app permissions | Yes |  |
| `vibe_set_app_permissions` | Set app permissions |  |  |

## Automations

| Tool | Purpose | Read-only | Notes |
|---|---|:---:|---|
| `automation_list` | List automations | Yes |  |
| `automation_create` | Create automation |  |  |
| `automation_get` | Get automation | Yes |  |
| `automation_rename` | Rename automation |  |  |
| `automation_delete` | Delete automation |  |  |
| `automation_duplicate` | Duplicate automation |  |  |
| `automation_set_active` | Set automation active |  |  |
| `automation_publish` | Publish automation |  |  |
| `automation_validate` | Validate automation | Yes |  |
| `automation_test_run` | Test automation |  | Skips tasks that change data. |
| `automation_test_run_write` | Test automation with writes |  | Runs every task, including ones that change data. |
| `automation_get_task_schema` | Get automation task schema | Yes |  |
| `automation_get_tasks` | Get automation tasks | Yes |  |
| `automation_add_tasks` | Add automation tasks |  |  |
| `automation_update_task` | Update automation task |  |  |
| `automation_test_task` | Test automation task |  | Only for tasks that do not change data. |
| `automation_test_task_write` | Test automation task that writes |  | Runs the task even if it changes data. |
| `automation_remove_tasks` | Remove automation tasks |  |  |
| `automation_move_tasks` | Move automation tasks |  |  |
| `automation_set_trigger` | Set automation trigger |  |  |
