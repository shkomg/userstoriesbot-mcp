# User Stories Bot MCP server

Let your agents record the demo. User Stories Bot records narrated demo videos of your app; its MCP server opens that pipeline to your AI agents. An MCP-capable agent asks for a narrated demo of a branch, follows the recording and hands the finished video, captions and user stories to your reviewers, with no pull request needed.

This repository holds documentation only. The server is hosted by User Stories Bot; its source is not in this repository and there is nothing to install or run locally.

- Endpoint: `https://app.userstoriesbot.com/api/mcp` (Streamable HTTP, POST)
- Authentication: `Authorization: Bearer <USB_MCP_KEY>`
- Tools: `demo_record`, `run_status`, `videos_list`, `stories_get`
- Registry name: `com.userstoriesbot/mcp`
- Full guide: https://userstoriesbot.com/docs/mcp

## Who can use it

Paid plans; one API key per agent. An org admin creates API keys in the dashboard; they work on the Pro and Team plans. Each key belongs to the org whose GitHub App installation covers the repository, so install the User Stories Bot GitHub App on the repository first: https://github.com/apps/userstoriesbot-com

## Get a key

1. Sign in and open API keys in the dashboard: https://app.userstoriesbot.com/dashboard/api-keys
2. Create a key and copy it once; it is shown a single time.
3. Paste it into your client with the snippets shown next to the key, or use the forms below.

Put your own key where a snippet shows `<USB_MCP_KEY>`, or export it as `USB_MCP_KEY` where a snippet reads `${USB_MCP_KEY}`. Never commit the key.

## The four tools

| Tool | What it does | Input | Output |
| --- | --- | --- | --- |
| `demo_record` | Record a narrated demo of a branch from a short brief and get a run id back. | `repo` (owner/repo), `branch`, `brief` (at most 1000 characters), optional `idempotencyKey` (at most 128 characters) | `runId`, `status: "queued"`, `dashboardUrl` |
| `run_status` | Follow a run stage by stage, from sandbox start to the finished video. Read only. | `runId` | `runId`, `status` (`queued`, `provisioning`, `running`, `succeeded`, `failed`, `cancelled`, `refused`), `stage`, `outcome` (`videos`, `no_demo`, `unsupported_stack`), `reason`, `createdAt`, `finishedAt`, `videos` (`name`, `url`, `captionsUrl`, `posterUrl`), `dashboardUrl`, `setupBrief` |
| `videos_list` | List the finished videos of a repository with their share links and captions. Read only. | `repo`, optional `limit` (1 to 50, default 20) and `cursor` | `repo`, `videos` (`kind` `guide` or `run`, `name`, `title`, `url`, `captionsUrl`, `posterUrl`, `durationSec`, `runId`, `recordedAt`, `public`), `nextCursor` |
| `stories_get` | Fetch the user stories and demo steps a run covered, as structured JSON. Read only. | `runId` | `runId`, `stories` (`file`, `id`, `title`, `markdown`), `steps` (`spec`, `title`, `segments` of `marker` and `text`) |

Every result carries the output above as structured content plus a one-line text summary. A refused call returns a reason instead of a run.

The brief says what to show, in plain words. It passes the same checks as a rerun request: no links, no commands, no instructions to the bot.

## Connect a client

The server URL is the same for every client.

### One-click install

- [Install in VS Code](https://insiders.vscode.dev/redirect?url=vscode%3Amcp%2Finstall%3F%257B%2522name%2522%253A%2522userstoriesbot%2522%252C%2522type%2522%253A%2522http%2522%252C%2522url%2522%253A%2522https%253A%252F%252Fapp.userstoriesbot.com%252Fapi%252Fmcp%2522%252C%2522headers%2522%253A%257B%2522Authorization%2522%253A%2522Bearer%2520%2524%257Binput%253Ausb_mcp_key%257D%2522%257D%252C%2522inputs%2522%253A%255B%257B%2522type%2522%253A%2522promptString%2522%252C%2522id%2522%253A%2522usb_mcp_key%2522%252C%2522description%2522%253A%2522User%2520Stories%2520Bot%2520API%2520key%2522%252C%2522password%2522%253Atrue%257D%255D%257D)
- [Install in VS Code Insiders](https://insiders.vscode.dev/redirect?url=vscode-insiders%3Amcp%2Finstall%3F%257B%2522name%2522%253A%2522userstoriesbot%2522%252C%2522type%2522%253A%2522http%2522%252C%2522url%2522%253A%2522https%253A%252F%252Fapp.userstoriesbot.com%252Fapi%252Fmcp%2522%252C%2522headers%2522%253A%257B%2522Authorization%2522%253A%2522Bearer%2520%2524%257Binput%253Ausb_mcp_key%257D%2522%257D%252C%2522inputs%2522%253A%255B%257B%2522type%2522%253A%2522promptString%2522%252C%2522id%2522%253A%2522usb_mcp_key%2522%252C%2522description%2522%253A%2522User%2520Stories%2520Bot%2520API%2520key%2522%252C%2522password%2522%253Atrue%257D%255D%257D)
- [Install in Cursor](https://cursor.com/en/install-mcp?name=userstoriesbot&config=eyJ1cmwiOiJodHRwczovL2FwcC51c2Vyc3Rvcmllc2JvdC5jb20vYXBpL21jcCIsImhlYWRlcnMiOnsiQXV0aG9yaXphdGlvbiI6IkJlYXJlciA8VVNCX01DUF9LRVk%2BIn19)
- [Install in Visual Studio](https://aka.ms/vs/mcp-install?%7B%22name%22%3A%22userstoriesbot%22%2C%22url%22%3A%22https%3A%2F%2Fapp.userstoriesbot.com%2Fapi%2Fmcp%22%7D)

VS Code asks for the key in a password prompt; in Cursor, replace the `<USB_MCP_KEY>` placeholder with your key; in Visual Studio, add the `Authorization` header afterwards.

### VS Code

`.vscode/mcp.json` (Visual Studio reads it too), where VS Code asks for the key in a password prompt:

```json
{ "servers": { "userstoriesbot": { "type": "http", "url": "https://app.userstoriesbot.com/api/mcp", "headers": { "Authorization": "Bearer ${input:usb_mcp_key}" } } }, "inputs": [{ "type": "promptString", "id": "usb_mcp_key", "description": "User Stories Bot API key", "password": true }] }
```

### Claude Code

```bash
claude mcp add --transport http userstoriesbot https://app.userstoriesbot.com/api/mcp --header "Authorization: Bearer <USB_MCP_KEY>"
```

Or a `.mcp.json` file at the project root:

```json
{ "mcpServers": { "userstoriesbot": { "type": "http", "url": "https://app.userstoriesbot.com/api/mcp", "headers": { "Authorization": "Bearer ${USB_MCP_KEY}" } } } }
```

### Cursor

`.cursor/mcp.json`:

```json
{ "mcpServers": { "userstoriesbot": { "url": "https://app.userstoriesbot.com/api/mcp", "headers": { "Authorization": "Bearer ${env:USB_MCP_KEY}" } } } }
```

### Copilot CLI

```bash
copilot mcp add --transport http userstoriesbot https://app.userstoriesbot.com/api/mcp --header "Authorization: Bearer <USB_MCP_KEY>"
```

Copilot CLI and VS Code also read the portable `~/.copilot/mcp-config.json`:

```json
{ "mcpServers": { "userstoriesbot": { "type": "http", "url": "https://app.userstoriesbot.com/api/mcp", "headers": { "Authorization": "Bearer ${USB_MCP_KEY}" }, "tools": ["*"] } } }
```

### Copilot coding agent

In the repository settings (Code, planning, and automation, then Copilot, then MCP servers), store the key as a secret named `COPILOT_MCP_USB_KEY` and add:

```json
{ "mcpServers": { "userstoriesbot": { "type": "http", "url": "https://app.userstoriesbot.com/api/mcp", "headers": { "Authorization": "Bearer $COPILOT_MCP_USB_KEY" }, "tools": ["demo_record", "run_status", "videos_list", "stories_get"] } } }
```

`run_status`, `videos_list` and `stories_get` are marked read only; `demo_record` is not, because it starts a run.

### Codex

```bash
export USB_MCP_KEY="<USB_MCP_KEY>"
codex mcp add userstoriesbot --url https://app.userstoriesbot.com/api/mcp --bearer-token-env-var USB_MCP_KEY
```

Or the `~/.codex/config.toml` table (shared by the Codex CLI, the desktop app, the IDE extension and the Codex agent in Xcode):

```toml
[mcp_servers.userstoriesbot]
url = "https://app.userstoriesbot.com/api/mcp"
bearer_token_env_var = "USB_MCP_KEY"
```

### Any other client

Any MCP client that supports Streamable HTTP with an `Authorization` header can connect. Most read this shape:

```json
{ "mcpServers": { "userstoriesbot": { "url": "https://app.userstoriesbot.com/api/mcp", "headers": { "Authorization": "Bearer <USB_MCP_KEY>" } } } }
```

The guide at https://userstoriesbot.com/docs/mcp covers JetBrains Junie and AI Assistant, Devin, Antigravity, Zed, Kiro, claude.ai custom connectors and Xcode.

### Test the connection

The MCP Inspector lists the tools your key can call:

```bash
npx --yes @modelcontextprotocol/inspector@2 --cli --transport http --server-url https://app.userstoriesbot.com/api/mcp --header "Authorization: Bearer <USB_MCP_KEY>" --method tools/list --format json
```

## Usage

Push the branch to GitHub first, then ask your agent, for example:

```text
Record a demo of this branch with User Stories Bot: show the new checkout flow. Wait for the run to finish, then give me the video link, the captions and the story titles.
```

A typical session: the agent calls `demo_record` with the repository, the branch and a short brief, polls `run_status` until it finishes, then fetches `videos_list` and `stories_get`.

1. `demo_record` with `{ "repo": "acme/app", "branch": "feature/checkout", "brief": "Show the new checkout flow." }` answers `run <run id> queued; poll run_status` with the `runId` and the `dashboardUrl`.
2. `run_status` with `{ "runId": "<run id>" }`, every 30 seconds, until the status is `succeeded`, `failed`, `cancelled` or `refused`. On `succeeded`, each entry of `videos` carries the video `url` and its `captionsUrl`. On `failed`, `no_demo` or `unsupported_stack`, a `setupBrief` tells your coding agent what to change in the repository.
3. `stories_get` with `{ "runId": "<run id>" }` returns the user stories and the demo steps the run covered; `videos_list` with `{ "repo": "acme/app" }` lists the repository's guides and earlier runs.

Use one `idempotencyKey` per request: a retry with the same key returns the earlier run (`already requested`) instead of starting a new one.

## Limits

A run started over MCP uses the credits of your plan like a run from a pull request. Calls are rate limited per key and per org; the limits for your plan are shown on the API keys page of the dashboard. An HTTP 401 means the key is missing or revoked; an HTTP 429 means wait a minute and retry.

## Links

- Documentation: https://userstoriesbot.com/docs/mcp
- Official MCP Registry entry: https://registry.modelcontextprotocol.io/v0.1/servers/com.userstoriesbot%2Fmcp/versions/latest
- Website: https://userstoriesbot.com
- Contact: info@userstoriesbot.com
