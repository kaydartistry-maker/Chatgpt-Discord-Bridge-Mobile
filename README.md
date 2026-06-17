# Discord MCP Bridge

Give your AI companion full Discord access: read, send, edit, delete, search, react, images, emojis, stickers, and voice messages. Works with ChatGPT (MCP) or any MCP-compatible client.

## Tools

| Tool | What it does | Confirm? |
|------|-------------|----------|
| `discord_read_messages` | Read messages with image/emote/embed vision | No |
| `discord_send` | Send a message (with optional reply) | No |
| `discord_send_image` | Send image via URL, base64, or data URI | No |
| `discord_search_messages` | Search messages across a server | No |
| `discord_add_reaction` | React to a message | No |
| `discord_edit_message` | Edit a sent message | No |
| `discord_delete_message` | Delete a message | **Yes** |
| `discord_typing` | Show typing indicator | No |
| `discord_list_emojis` | List custom server emojis | No |
| `discord_list_stickers` | List server stickers | No |
| `discord_send_sticker` | Send a sticker | No |
| `discord_send_voice` | Voice message via ElevenLabs TTS | No |
| `discord_list_servers` | List all servers the bot is in | No |
| `discord_get_server_info` | Get server details + channels | No |

## Endpoints

- `/mcp` — JSON-RPC (POST)
- `/sse` — Server-Sent Events for ChatGPT MCP connection
- `/` — Health check / tool list

## Setup

### 1. Create Your Discord Bot

1. [Discord Developer Portal](https://discord.com/developers/applications) → New Application
2. Name it whatever you want — this is your companion's bot identity
3. Bot → Add Bot → copy token (this is your bot's password — don't share it)
4. Enable **MESSAGE CONTENT INTENT** under Privileged Gateway Intents

### 2. Invite to Your Server

1. OAuth2 → URL Generator → scope `bot`
2. Permissions: View Channels, Read Message History, Send Messages, Embed Links, Attach Files
3. Copy the generated URL → open in new tab → select your server → Authorize

### 3. Get Channel IDs

1. Discord → User Settings → Advanced → enable **Developer Mode**
2. Right-click any channel → Copy Channel ID

### 4. Clone and Deploy

```bash
git clone https://github.com/kaydartistry-maker/jaces-discord.git my-companion-discord
cd my-companion-discord
npm install
```

Rename the worker in `wrangler.toml` if you want:

```toml
name = "my-companion-discord"
```

Set your bot token and deploy:

```bash
npx wrangler secret put DISCORD_TOKEN
npx wrangler deploy
```

### 5. Connect to ChatGPT

1. Settings → Apps → Advanced settings → enable **Developer mode**
   - Note: this disables GPT memory — that's expected, not a bug
2. Create app:
   - **Name:** whatever you want
   - **MCP URL:** `https://your-worker-name.your-subdomain.workers.dev/sse`
   - **Auth:** No Auth

### 6. Voice (Optional)

1. Create a voice at [ElevenLabs](https://elevenlabs.io) and copy the voice ID
2. Open `src/worker.js` and find the `VOICE_MAP` near the top — replace the key with your companion's name and paste the voice ID
3. Set the secret and redeploy:

```bash
npx wrangler secret put ELEVENLABS_API_KEY
npx wrangler deploy
```

## ChatGPT Confirm Gate

All tools use MCP `annotations` to bypass ChatGPT's default "consequential action" confirm prompt. Only `discord_delete_message` keeps the gate. If ChatGPT still prompts on send/edit/etc, delete and recreate the app in Settings to force a fresh tool discovery.

## Troubleshooting

| Problem | Fix |
|---|---|
| Discord error 403 | Bot lacks channel permissions — Server Settings → Roles |
| Can't see message content | MESSAGE CONTENT INTENT not enabled in Developer Portal |
| 401 / Missing token | Re-set via `npx wrangler secret put DISCORD_TOKEN` |
| Voice says "not configured" | Add voice ID to VOICE_MAP in worker.js and redeploy |
| ChatGPT doesn't see new tools | Delete and recreate the MCP app in ChatGPT Settings |
