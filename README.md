# Jace's Discord MCP Bridge

Full-featured Discord MCP worker for ChatGPT. 14 tools, voice-ready, zero dependencies.

**Live:** `jaces-discord-tool.kaydartistry.workers.dev`

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

### 1. Discord Bot

1. [Discord Developer Portal](https://discord.com/developers/applications) → New Application
2. Bot → Add Bot → copy token
3. Enable **MESSAGE CONTENT INTENT**
4. OAuth2 → URL Generator → scope `bot` → permissions: View Channels, Read Message History, Send Messages, Embed Links, Attach Files
5. Invite to your server

### 2. Deploy

```bash
cd ~/jaces-discord
npm install
npx wrangler deploy
```

### 3. Secrets

```bash
npx wrangler secret put DISCORD_TOKEN
npx wrangler secret put ELEVENLABS_API_KEY  # when ready for voice
```

### 4. Connect to ChatGPT

1. Settings → Apps → Advanced settings → enable Developer mode
2. Create app:
   - **Name:** `jaces-discord-bridge`
   - **MCP URL:** `https://jaces-discord-tool.kaydartistry.workers.dev/sse`
   - **Auth:** No Auth

### 5. Voice (Optional)

Add the ElevenLabs voice ID to `src/worker.js` line 12:

```js
const VOICE_MAP = {
  jace: "YOUR_VOICE_ID_HERE",
};
```

Then redeploy: `npx wrangler deploy`

## ChatGPT Confirm Gate

All tools use MCP `annotations` to bypass ChatGPT's default "consequential action" confirm prompt. Only `discord_delete_message` keeps the gate. If ChatGPT still prompts on send/edit/etc, delete and recreate the app in Settings to force a fresh tool discovery.
