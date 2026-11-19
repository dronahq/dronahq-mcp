
# Building with DronaHQ MCP

- Never put connection strings, API keys, or passwords in app source, prompts, or chat. Data goes through connectors stored in DronaHQ.
- Start with `vibe_list_connectors`. If the connector you need is missing, call `vibe_request_connector_setup` and give the user the returned link. Poll `vibe_get_connector_job` until it finishes. Do not ask the user to paste secrets.
- Before writing SQL, call `vibe_prepare_sql_query_instructions` for that connector. Use the returned schema and dialect notes, not guessed table names.
- Explore with `vibe_run_db_query`, which is read-only. Use `vibe_run_db_write` only when the user asked for a data change.
- App code should call saved operations (`vibe_create_subcat`, `vibe_create_http_read_subcat`, `vibe_create_http_write_subcat`), not raw SQL. Bind the connectors to the app with `vibe_bind_connectors`.
- After editing files, call `vibe_save_app` and share `vibe_preview_url` with the user.
- Call `vibe_check_in` before `vibe_publish`. Ask the user which catalogue to publish to and whether the link should be public or private (`vibe_set_access`).
- Test automations with `automation_test_run` first. Use `automation_test_run_write` only when the user agrees to real data changes.
