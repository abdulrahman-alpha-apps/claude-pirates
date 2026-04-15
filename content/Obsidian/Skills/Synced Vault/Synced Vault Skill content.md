---
publish: true
description: Set up an Obsidian vault with full Claude/MCP integration. Use this skill whenever the user wants to connect Obsidian to Claude, install the MCP Tools plugin, set up Local REST API, enable Smart Connections, configure their Obsidian vault for AI access, sync a vault, or expose Obsidian to the web. Trigger on phrases like "set up obsidian", "connect obsidian to claude", "install obsidian plugins", "obsidian mcp", "synced vault", or any mention of wanting Claude to read/write their Obsidian notes.
created: 2026-04-14T16:10:56.311+03:00
modified: 2026-04-15T14:16:47.589+03:00
---

# Synced Vault — Obsidian MCP Environment Setup

This skill installs and configures three Obsidian plugins that together give Claude full access to the user's vault, then optionally exposes that access over the web.

## Plugins to Install

| Plugin ID | Purpose |
|---|---|
| `obsidian-local-rest-api` | HTTP REST API with API key auth — the foundation everything else uses |
| `obsidian-smart-connections` | Semantic/vector search across vault notes |
| `mcp-tools` | MCP server binary that bridges Claude Desktop to the vault |

## Step 1 — Identify the Vault

The vault is the **current working directory** where this skill is invoked. Use the current working directory as `VAULT_PATH`.

Verify it looks like an Obsidian vault by checking for a `.obsidian` directory:

```bash
ls -d .obsidian 2>/dev/null
```

If `.obsidian` doesn't exist, this folder hasn't been opened in Obsidian yet. Tell the user:

> "This folder isn't an Obsidian vault yet. Please open Obsidian → Open folder as vault → select this directory, then run this skill again."

Once confirmed, get the vault name from Obsidian:

```bash
VAULT_PATH="$(pwd)"
VAULT_NAME=$(obsidian vaults verbose 2>/dev/null | grep "$VAULT_PATH" | cut -f1)
```

If the vault isn't registered with Obsidian (no match), tell the user to open it in Obsidian first.

If the path has spaces, quote it in all subsequent commands.

## Step 2 — Install and Enable the Plugins

Run all three installs. The `enable` flag activates them immediately:

```bash
obsidian vault=<VaultName> plugin:install id=obsidian-local-rest-api enable
obsidian vault=<VaultName> plugin:install id=obsidian-smart-connections enable
obsidian vault=<VaultName> plugin:install id=mcp-tools enable
```

If a plugin is already installed, `plugin:install` will skip gracefully. If any install fails, report the error and ask the user to check that Obsidian is open and the vault is accessible.

After installing, run `obsidian vault=<VaultName> plugins:enabled` to confirm all three appear in the enabled list.

### IMPORTANT: Restart Obsidian After Plugin Install

After installing the plugins, **Obsidian MUST be restarted** before proceeding. The Local REST API plugin generates its authentication key on load, and the running instance won't recognize the new plugins or their keys until restarted.

Run:

```bash
obsidian restart
```

Then wait a few seconds for Obsidian to fully reload before continuing.

### Install the MCP Server Binary (Manual Step)

The MCP Tools plugin does **not** ship its server binary pre-installed. The user must install it manually from within Obsidian after the plugin is enabled:

1. Open **Settings** (gear icon)
2. Go to **Community Plugins → MCP Tools** (click the gear/options icon next to it)
3. Click **"Install server"**

**Ask the user to confirm they have completed this step before proceeding.** The MCP server binary will not exist until this is done, and the Claude Desktop config will not work without it.

## Step 3 — Retrieve the API Key

The Local REST API plugin generates an API key on first load. Read it from the plugin's data file:

```bash
cat "$VAULT_PATH/.obsidian/plugins/obsidian-local-rest-api/data.json" | python3 -c "import sys,json; d=json.load(sys.stdin); print(d.get('apiKey',''))"
```

If the key is empty, Obsidian may not have loaded the plugin yet. Ask the user to:

1. Restart Obsidian (or run `obsidian restart`)
2. Then re-run the cat command above

Store the key as `API_KEY`.

## Step 4 — Verify Everything Works

Hit the Local REST API to confirm it's running and authenticated:

```bash
curl -sk -H "Authorization: Bearer $API_KEY" https://127.0.0.1:27124/ | python3 -m json.tool
```

A successful response looks like:

```json
{
  "status": "OK",
  "service": "Obsidian Local REST API",
  "authenticated": true
}
```

If `authenticated` is `false`, the API key does not match what the running Obsidian instance has in memory. This almost always means **Obsidian was not restarted** after the plugin was installed or the key was regenerated. Fix by:

1. Restarting Obsidian (`obsidian restart`)
2. Waiting a few seconds for it to fully load
3. Re-reading the API key from `data.json`
4. Retrying the curl command with the fresh key

If the connection is refused, the plugin isn't running — confirm Obsidian is open.

## Step 5 — Show Local MCP Config

Display the ready-to-use Claude Desktop config for local (non-web) access:

```
Add this to your claude_desktop_config.json → mcpServers:

"obsidian-mcp-tools": {
  "command": "<VAULT_PATH>/.obsidian/plugins/mcp-tools/bin/mcp-server",
  "env": {
    "OBSIDIAN_API_KEY": "<API_KEY>"
  }
}
```

Note: if the vault path has spaces, the command path must still be the full absolute path. Claude Desktop handles it correctly.

## Step 6 — Web Exposure (Optional)

This step is **entirely optional**. After local setup is verified, ask the user:

> "Everything is set up locally. Would you also like to expose your Obsidian vault over the web? This is optional — it creates a public URL so you can connect from other devices or share access. Most users don't need this."

Wait for the user's answer. If **yes**, proceed with the web setup below. If **no**, skip to the final summary. **Do not proceed with web exposure unless the user explicitly opts in.**

### Web Exposure Setup

**Terminal 1 — Start SSE server** (wraps the MCP binary as an HTTP SSE endpoint):

```bash
OBSIDIAN_API_KEY=<API_KEY> npx -y supergateway \
  --stdio "<VAULT_PATH>/.obsidian/plugins/mcp-tools/bin/mcp-server" \
  --port 8765
```

Run this in the background and wait 3 seconds, then verify it's live:

```bash
curl -s --max-time 3 http://localhost:8765/sse | head -2
```

Expected: `event: endpoint` on the first line.

**Terminal 2 — Start public tunnel:**

```bash
ssh -R 80:localhost:8765 localhost.run 2>&1 | tee /tmp/lhr_tunnel.txt &
sleep 10 && grep -oE 'https://[a-z0-9]+\.lhr\.life' /tmp/lhr_tunnel.txt | tail -1
```

The URL printed (e.g. `https://abc123.lhr.life`) is the public endpoint.

**Output to the user:**

```
Public SSE URL:  https://<tunnel-id>.lhr.life/sse

Claude Desktop config (web version):

"obsidian-mcp-tools-web": {
  "command": "npx",
  "args": [
    "-y",
    "supergateway",
    "--sse",
    "https://<tunnel-id>.lhr.life/sse"
  ]
}
```

Warn the user: this URL changes each session on the free tier. They can get a permanent domain at https://admin.localhost.run.

## Final Summary

After completing setup, give the user a clean summary:

```
✅ Obsidian MCP Environment Ready

Plugins installed:
  • Local REST API  (https://127.0.0.1:27124)
  • Smart Connections
  • MCP Tools

API Key: <first 8 chars>...
Local config: added to claude_desktop_config.json
Web URL: <url or "not configured">

Next step: restart Claude Desktop to load the new MCP server.
```

## Troubleshooting

| Symptom                   | Likely cause               | Fix                                             |
| ------------------------- | -------------------------- | ----------------------------------------------- |
| `plugin:install` fails    | Obsidian not open          | Open Obsidian first                             |
| API key is empty          | Plugin not loaded yet      | Restart Obsidian                                |
| `curl` connection refused | Local REST API not running | Check plugin is enabled in Obsidian settings    |
| `authenticated: false`    | Wrong API key              | Re-read data.json after restart                 |
| 502 on tunnel URL         | Tunnel session expired     | Re-run `ssh -R 80:localhost:8765 localhost.run` |
| Spaces in vault path      | Command breaks             | Always quote the full path                      |
