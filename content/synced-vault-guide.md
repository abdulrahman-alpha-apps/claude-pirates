---
title: Synced Vault Guide
publish: true
---

## What This Skill Does

The **synced-vault** skill connects your Obsidian vault to Claude by installing three plugins:

| Plugin | Purpose |
|--------|---------|
| Local REST API | Gives Claude HTTP access to read/write your vault |
| Smart Connections | Semantic search across your notes |
| MCP Tools | Bridges Claude Desktop directly to your vault |

After setup, Claude can read, search, and write notes in your vault — from Claude Code or Claude Desktop.

---

## Prerequisites

- Obsidian is **open** and the vault is loaded
- The **Terminal** plugin is installed in Obsidian (to run Claude Code inside Obsidian)

> [!Note]
> If you don't have the Terminal plugin yet, install it from Community Plugins inside Obsidian before continuing.

---

## Step 1 — Install the Skill

Attach the skill content to Claude Code, or copy it manually, using the prompt below.  
The skill file is here: [[Synced Vault Skill content]]

> [!Prompt]
> Install this skill globally

---

## Step 2 — Run the Skill

Open Obsidian → open the Terminal plugin → run Claude Code inside your vault directory, then trigger the skill:

> [!Prompt]
> Run synced-vault skill in this directory

Claude will install the plugins, retrieve the API key, verify the connection, and give you the MCP config for Claude Desktop.

---

## What You'll Need to Do (Manual Steps)

Claude handles almost everything automatically. The only actions you may need to take:

| # | Action | When |
|---|--------|------|
| 1 | **Restart Obsidian** after plugins install | Required — Obsidian must load the plugins to generate the API key |
| 2 | In Obsidian Settings → MCP Tools → click **"Install server"** | Required — the MCP binary must be installed from inside Obsidian |

---

## What You Get After Setup

- Claude can read and write any note in your vault via the REST API
- Claude Desktop gets an MCP config entry so it connects directly to your vault
- Smart Connections adds semantic search so Claude can find related notes
- (Optional) A public web URL so you can connect from other devices
