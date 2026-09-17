# Security

## Secrets

All credentials live in Cloudflare Worker secrets — never commit them:

- `DISCORD_TOKEN` — your Discord bot token
- `MCP_SECRET` — random string used as URL-path auth (generate with `openssl rand -hex 24`)
- `ELEVENLABS_API_KEY` — optional, for voice notes
- `VOICE_MAP` — optional JSON mapping voice names to ElevenLabs voice IDs

Set each with `wrangler secret put <NAME>`. The `.gitignore` excludes `.dev.vars`, `.env*`, and key files; keep it that way.

## Rotation

If any secret may have leaked (screenshot, pasted URL, shared config):

1. `wrangler secret put MCP_SECRET` with a fresh value — old URLs die instantly.
2. Regenerate the Discord bot token in the Discord developer portal.
3. Rotate the ElevenLabs key from the ElevenLabs dashboard.

## Reporting

This is a small personal-use project. If you find a vulnerability, open a GitHub issue without exploit details and ask for a private contact channel.
