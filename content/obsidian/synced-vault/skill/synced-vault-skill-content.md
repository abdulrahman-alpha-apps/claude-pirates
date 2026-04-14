---
title: Synced Vault Skill Content
publish: true
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

```bash
VAULT_PATH="$(pwd)"
ls -d .obsidian 2>/dev/null
```

## Step 2 — Install Plugins

```bash
obsidian vault=<VaultName> plugin:install id=obsidian-local-rest-api enable
obsidian vault=<VaultName> plugin:install id=obsidian-smart-connections enable
obsidian vault=<VaultName> plugin:install id=mcp-tools enable
```

Restart Obsidian after install.

## Step 3 — Get API Key

```bash
cat "$VAULT_PATH/.obsidian/plugins/obsidian-local-rest-api/data.json" \
  | python3 -c "import sys,json; d=json.load(sys.stdin); print(d.get('apiKey',''))"
```

## Step 4 — Verify

```bash
curl -sk -H "Authorization: Bearer $API_KEY" https://127.0.0.1:27124/
```

Expected: `"authenticated": true`

## Step 5 — Local MCP Config

```json
"obsidian-mcp-tools": {
  "command": "<VAULT_PATH>/.obsidian/plugins/mcp-tools/bin/mcp-server",
  "env": { "OBSIDIAN_API_KEY": "<API_KEY>" }
}
```

## Step 6 — Web Exposure (Optional)

```bash
OBSIDIAN_API_KEY=<API_KEY> npx -y supergateway \
  --stdio "<VAULT_PATH>/.obsidian/plugins/mcp-tools/bin/mcp-server" --port 8765

ssh -R 80:localhost:8765 localhost.run 2>&1 | tee /tmp/lhr_tunnel.txt &
sleep 10 && grep -oE 'https://[a-z0-9]+\.lhr\.life' /tmp/lhr_tunnel.txt | tail -1
```

## Troubleshooting

| Symptom | Fix |
|---|---|
| API key is empty | Restart Obsidian |
| `curl` connection refused | Check plugin is enabled |
| `authenticated: false` | Re-read data.json after restart |
| 502 on tunnel URL | Re-run the ssh tunnel command |
