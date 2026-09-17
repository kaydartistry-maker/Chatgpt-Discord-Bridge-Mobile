# Privacy

This project is a self-hosted Discord MCP bridge for ChatGPT you deploy to your own Cloudflare Workers account. No data is sent to the project authors.

## What flows where

- **Your ChatGPT client → your Worker**: MCP tool calls travel over HTTPS to the Worker URL you deploy. The URL contains your `MCP_SECRET` as a path segment — treat the full URL as a credential.
- **Your Worker → Discord**: the Worker calls the Discord REST API using your bot token (stored as a Worker secret, never in code).
- **Your Worker → ElevenLabs** (optional): voice-note text is sent to ElevenLabs for TTS if you configure `ELEVENLABS_API_KEY` and `VOICE_MAP`.

## What is stored

Nothing. The Worker is stateless — no database, no logging of message content by this codebase. Cloudflare's own request logs are governed by your Cloudflare account settings.

## Bearer-URL risk

Because the secret rides in the URL path, anyone who sees the full URL (browser history, shared screenshots, proxy logs) can call your bridge. If the URL leaks, rotate `MCP_SECRET` immediately (`wrangler secret put MCP_SECRET`) and update your client config.

## Least privilege

Give the Discord bot only the permissions and channel access it actually needs. Prefer a dedicated bot per deployment; don't reuse a bot token across projects.
