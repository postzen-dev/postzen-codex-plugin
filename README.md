# PostZen plugin for OpenAI Codex

Schedule and publish social media posts across 10 platforms — X (Twitter), Instagram, TikTok, LinkedIn, Facebook, YouTube, Threads, Pinterest, Bluesky, and Telegram — without leaving Codex.

The plugin connects Codex to the hosted [PostZen MCP server](https://mcp.postzen.dev) and adds workflow skills on top of it: drafting and adapting content per platform, uploading media, filling posting queues, connecting accounts, and building analytics reports.

## Installation

Install from the community Codex Plugin Marketplace CLI:

```bash
npx codex-marketplace add postzen-dev/postzen-codex-plugin --plugin
```

The CLI prompts for project or global scope. Add `--project` or `--global` to skip the prompt.

Or clone the plugin repository and add it to `$REPO_ROOT/.agents/plugins/marketplace.json` for a repo-scoped list, or `~/.agents/plugins/marketplace.json` for a personal one:

```json
{
  "name": "postzen-plugins",
  "interface": { "displayName": "PostZen" },
  "plugins": [
    {
      "name": "postzen",
      "source": { "source": "local", "path": "./plugins/postzen" },
      "policy": { "installation": "AVAILABLE", "authentication": "ON_INSTALL" },
      "category": "Productivity"
    }
  ]
}
```

Here `./plugins/postzen` is the cloned repository. Then enable PostZen from Codex's plugin list.

On first use, Codex prompts you to sign in to PostZen in the browser. Approve access and choose which profiles to expose; there is no API key to paste.

Don't have a PostZen account? Sign up at [postzen.dev](https://www.postzen.dev) and connect your social accounts in the [dashboard](https://app.postzen.dev) — or let Codex walk you through it with the `postzen-connect` skill.

### Headless / CI use

For non-interactive environments, configure the MCP server directly in `~/.codex/config.toml` with a PostZen API key instead of the plugin:

```toml
[mcp_servers.postzen]
url = "https://mcp.postzen.dev/mcp"
bearer_token_env_var = "POSTZEN_API_KEY"
```

Export `POSTZEN_API_KEY=pzn_...`; create the key in the dashboard under Settings → API keys.

## Skills

| Skill | What it does |
|---|---|
| `postzen-post` | Draft, adapt, and publish or schedule a post across platforms, with media uploads, queue slots, and best-time-to-post suggestions |
| `postzen-queue` | Set up recurring posting slots (at your best-performing times), preview upcoming posts, and fill the queue |
| `postzen-analytics` | A readable analytics summary: post performance, follower growth, daily metrics |
| `postzen-connect` | Guided flow to link a new social account |

## Tools

The MCP server exposes 44 tools; Codex picks them up automatically. The main groups:

| Group | Tools |
|---|---|
| Posts | `createPost`, `listPosts`, `getPost`, `updatePost`, `deletePost`, `createMediaPresign` |
| Queues | `listQueueSlots`, `createQueueSlot`, `updateQueueSlot`, `deleteQueueSlot`, `getNextQueueSlot`, `previewQueue` |
| Accounts | `listAccounts`, `createConnectUrl`, `completeConnect`, `disconnectAccount`, Pinterest board tools |
| Analytics | `getAnalytics`, `getPostTimeline`, `getDailyMetrics`, `getFollowerStats`, `getBestTimeToPost`, `syncExternalPosts` |
| Profiles, API keys, webhooks | `listProfiles`, `createProfile`, `listApiKeys`, `createWebhook`, `listWebhookDeliveries`, and more |

## Example prompts

```text
Post this launch announcement to X and LinkedIn tomorrow at 9am PT
```

```text
Take this blog post and turn it into a thread for X and a LinkedIn post, save both as drafts
```

```text
How did my posts perform this week?
```

```text
Add these three posts to my queue
```

```text
Connect my Pinterest account
```

## Safety

Publishing is an outward-facing action: the skills always show you the final per-platform content and timing and ask for confirmation before anything goes live. Drafts never require confirmation. The skills never ask for social media passwords or tokens — account connections always go through the platform's own browser-based flow.

## Authentication, network access, and data

- The plugin connects only to PostZen's hosted MCP endpoint at `https://mcp.postzen.dev/mcp`, which proxies tool calls to the PostZen API at `https://api.postzen.dev`.
- Authentication is handled through the MCP OAuth flow (sign in to your PostZen account in the browser). The access token carries a PostZen API key scoped to the profiles you approved; you can revoke it at any time in the PostZen dashboard.
- Media uploads go to the presigned upload URL returned by `createMediaPresign` — nothing else on your machine is read.
- No hooks, no shell commands, no local code execution: the plugin is skills plus a remote MCP server.

## Development

```bash
git clone https://github.com/postzen-dev/postzen-codex-plugin
```

## Links

- [PostZen](https://www.postzen.dev) · [Dashboard](https://app.postzen.dev) · [API docs](https://docs.postzen.dev) · [Status](https://status.postzen.dev)
- Prefer a raw API? See the [agent quickstart](https://www.postzen.dev/agent-quickstart.md), the [Node SDK](https://www.npmjs.com/package/@postzen/node), [Python SDK](https://pypi.org/project/postzen-sdk/), or [CLI](https://www.npmjs.com/package/@postzen/cli).
- Using Claude Code instead? See the [PostZen Claude Code plugin](https://github.com/postzen-dev/postzen-claude-plugin).

## License

MIT
