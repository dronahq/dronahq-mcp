# Connect a client

DronaHQ MCP is a remote MCP server using Streamable HTTP, with sign-in through MCP authorization (OAuth). Any client that supports both connects with only the URL. There's no API key or package to install.

```
https://studio.dronahq.com/api/vibe/mcp
```

If you're self-hosted or in a VPC, replace `studio.dronahq.com` with your Studio host. To pin one MCP server, copy the `/api/vibe/mcp/{serverId}` URL from **Connect DronaHQ MCP** in Studio.

The first time a client connects, the server answers `401` and the client opens your browser. Sign in the same way you sign in to Studio (password, Google, or SSO/SAML) and approve the client once. After that, the client refreshes its own access.

| Client | Setup |
|---|---|
| Cursor | [One click or `mcp.json`](#cursor) |
| VS Code and GitHub Copilot | [One click or `mcp.json`](#vs-code-and-github-copilot) |
| Claude (web and desktop) | [Custom connector](#claude-web-and-desktop) |
| Claude Code | [`claude mcp add`](#claude-code) |
| ChatGPT | [Developer mode connector](#chatgpt) |
| OpenAI Codex | [`config.toml`](#openai-codex) |
| Gemini CLI | [`settings.json`](#gemini-cli) |
| GitHub Copilot CLI | [`mcp-config.json`](#github-copilot-cli) |
| Windsurf | [`mcp_config.json`](#windsurf) |
| Zed | [`settings.json`](#zed) |
| JetBrains IDEs | [AI Assistant settings](#jetbrains-ides) |
| Kiro | [`mcp.json`](#kiro) |
| Amazon Q Developer | [`mcp.json`](#amazon-q-developer) |
| Cline | [MCP settings](#cline) |
| Continue | [`config.yaml`](#continue) |
| Goose | [Remote extension](#goose) |
| OpenCode | [`opencode.json`](#opencode) |
| Anything else | [Remote URL, or the `mcp-remote` bridge](#any-other-client) |

Client settings change often. If a field name below no longer matches, follow that client's docs for adding a remote MCP server over HTTP and use the URL above.

## Cursor

Use **Add to Cursor** in the [README](../README.md) or the Studio drawer. Or add this to `.cursor/mcp.json` in a project, or to `~/.cursor/mcp.json` for every project:

```json
{
  "mcpServers": {
    "dronahq": {
      "url": "https://studio.dronahq.com/api/vibe/mcp"
    }
  }
}
```

Open **Settings → MCP**, click **Connect** next to `dronahq`, and sign in.

## VS Code and GitHub Copilot

Use **Install in VS Code** in the [README](../README.md) or the Studio drawer. Or add this to `.vscode/mcp.json`:

```json
{
  "servers": {
    "dronahq": {
      "type": "http",
      "url": "https://studio.dronahq.com/api/vibe/mcp"
    }
  }
}
```

Start the server from the file or from **MCP: List Servers**, then sign in when VS Code asks. The tools appear in Copilot Chat agent mode.

## Claude (web and desktop)

This works in claude.ai, Claude Desktop, and Cowork, on every plan. The Free plan allows one custom connector.

1. Open [**Customize → Connectors**](https://claude.ai/customize/connectors).
2. Select **Add**, then **Add custom connector**.
3. Enter `DronaHQ` as the name and paste the server URL, then click **Continue**.
4. Keep the authentication options marked **Detected**, then click **Add**.
5. Click **Connect** and sign in with your DronaHQ account.

**Team and Enterprise:** members can't add custom connectors themselves. An owner adds it once in [**Organization settings → Connectors**](https://claude.ai/admin-settings/connectors) (**Add → Custom → Web**, same name and URL). Members then find **DronaHQ** with the **Custom** label under **Customize → Connectors** and click **Connect**.

Claude connects from Anthropic's cloud, not from your computer. A self-hosted Studio must be reachable from the internet, or exposed through an [MCP tunnel](https://claude.com/docs/connectors/mcp-tunnels/overview). Otherwise, use Claude Code or Claude Desktop with the [`mcp-remote` bridge](#any-other-client).

## Claude Code

```bash
claude mcp add --transport http dronahq -s user "https://studio.dronahq.com/api/vibe/mcp"
```

Run `/mcp`, pick `dronahq`, and choose **Authenticate**.

## ChatGPT

On plans with developer mode:

1. Open **Settings → Apps & Connectors → Advanced settings** and turn on **Developer mode**.
2. Create a connector, paste the server URL, and choose OAuth for authentication.
3. Sign in when ChatGPT opens the browser, then enable the connector in a chat.

Workspace admins on Business, Enterprise, and Edu plans control whether members can add custom connectors.

## OpenAI Codex

Add this to `~/.codex/config.toml`:

```toml
[mcp_servers.dronahq]
url = "https://studio.dronahq.com/api/vibe/mcp"
```

Then sign in:

```bash
codex mcp login dronahq
```

## Gemini CLI

Add this to `~/.gemini/settings.json`, or to `.gemini/settings.json` in a project:

```json
{
  "mcpServers": {
    "dronahq": {
      "httpUrl": "https://studio.dronahq.com/api/vibe/mcp"
    }
  }
}
```

Run `/mcp auth dronahq` in Gemini CLI to sign in.

## GitHub Copilot CLI

Run `/mcp add` and choose HTTP, or add this to `~/.copilot/mcp-config.json`:

```json
{
  "mcpServers": {
    "dronahq": {
      "type": "http",
      "url": "https://studio.dronahq.com/api/vibe/mcp"
    }
  }
}
```

## Windsurf

Use **Open in Windsurf** in the Studio drawer, or add this to `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "dronahq": {
      "serverUrl": "https://studio.dronahq.com/api/vibe/mcp"
    }
  }
}
```

## Zed

Add this to Zed's `settings.json`:

```json
{
  "context_servers": {
    "dronahq": {
      "url": "https://studio.dronahq.com/api/vibe/mcp"
    }
  }
}
```

## JetBrains IDEs

In IntelliJ IDEA, PyCharm, WebStorm, and the other JetBrains IDEs, open **Settings → Tools → AI Assistant → Model Context Protocol (MCP)**. Add a server, choose HTTP, and paste the URL. If your version only accepts command-based servers, use the [`mcp-remote` bridge](#any-other-client).

## Kiro

Add this to `.kiro/settings/mcp.json` in a project, or to `~/.kiro/settings/mcp.json` for every project:

```json
{
  "mcpServers": {
    "dronahq": {
      "url": "https://studio.dronahq.com/api/vibe/mcp"
    }
  }
}
```

## Amazon Q Developer

Add this to `~/.aws/amazonq/mcp.json`:

```json
{
  "mcpServers": {
    "dronahq": {
      "type": "http",
      "url": "https://studio.dronahq.com/api/vibe/mcp"
    }
  }
}
```

## Cline

Open **MCP Servers → Remote Servers**, name the server `dronahq`, and paste the URL. Or edit `cline_mcp_settings.json`:

```json
{
  "mcpServers": {
    "dronahq": {
      "type": "streamableHttp",
      "url": "https://studio.dronahq.com/api/vibe/mcp"
    }
  }
}
```

## Continue

Add this to `config.yaml`:

```yaml
mcpServers:
  - name: dronahq
    type: streamable-http
    url: https://studio.dronahq.com/api/vibe/mcp
```

## Goose

Run `goose configure`, choose **Add Extension → Remote Extension (Streamable HTTP)**, name it `dronahq`, and paste the URL.

## OpenCode

Add this to `opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "dronahq": {
      "type": "remote",
      "url": "https://studio.dronahq.com/api/vibe/mcp"
    }
  }
}
```

## Any other client

If the client supports remote MCP servers over HTTP with OAuth, give it the URL.

If it only runs local (stdio) servers, use [`mcp-remote`](https://www.npmjs.com/package/mcp-remote). It runs locally, forwards to DronaHQ, and handles the browser sign-in. It needs Node.js 18 or later.

```json
{
  "mcpServers": {
    "dronahq": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://studio.dronahq.com/api/vibe/mcp"]
    }
  }
}
```

## Troubleshooting

- **No browser window opens.** The client may not support MCP authorization. Use the `mcp-remote` bridge.
- **Signed in to the wrong workspace.** Disconnect the client in the Studio drawer, then connect again and choose the workspace at sign-in. Or use the pinned `/api/vibe/mcp/{serverId}` URL.
- **Tools are missing.** You see only the tools and connectors your Studio role allows. End users can't use this server. Ask an owner or admin to grant connectors to you or your group.
- **Self-hosted host can't be reached.** Your machine must reach the Studio host directly, for example over VPN.
