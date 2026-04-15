---
publish: true
title: Synced Vault Guide
created: 2026-04-14T17:37:34.977+03:00
modified: 2026-04-15T13:27:44.946+03:00
---

## What This Skill Does

The **synced-vault** skill connects your Obsidian vault to Claude by installing three plugins:

| Plugin | Purpose |
|--------|---------|
| Local REST API | Gives Claude HTTP access to read/write your vault |
| Smart Connections | Semantic search across your project documents and vault content |
| MCP Tools | Bridges Claude Desktop directly to your vault |

After setup, Claude can read, search, and write vault files and project documents — from Claude Code or Claude Desktop.

This matters because Claude Code is more than a chat tool here. It becomes an agent workspace where you can run repeatable workflows and build focused agents that operate directly on your project information layer.

---

## Prerequisites

- Obsidian is **open** and the vault is loaded
- The **Terminal** plugin is installed in Obsidian (to run Claude Code inside Obsidian)

> [!Note]
> If you don't have the Terminal plugin yet, install it from Community Plugins inside Obsidian before continuing.

---

## Step 1 — Install the Skill

Attach the skill content to Claude Code, or copy it manually, using the prompt below.\
The skill file is here: [[Synced Vault Skill content]]

> [!Prompt]
> Install this skill globally

---

## Step 2 — Run the Skill

Open Obsidian → open the Terminal plugin → run Claude Code inside your vault directory, then trigger the skill:

> [!Prompt]
> Run synced-vault skill in this directory

Claude will install the plugins, retrieve the API key, verify the connection, and give you the MCP config for Claude Desktop.

Once connected, you can use Claude Code not only to help manually, but to power task-specific agents that sync, organize, search, maintain, and extend your vault with much less repeated effort.

---

## What You'll Need to Do (Manual Steps)

Claude handles almost everything automatically. The only actions you may need to take:

| # | Action | When |
|---|--------|------|
| 1 | **Restart Obsidian** after plugins install | Required — Obsidian must load the plugins to generate the API key |
| 2 | In Obsidian Settings → MCP Tools → click **"Install server"** | Required — the MCP binary must be installed from inside Obsidian |

---

## What You Get After Setup

- Claude can read and write project files and vault documents via the REST API
- Claude Desktop gets an MCP config entry so it connects directly to your vault
- Smart Connections adds semantic search so Claude can find related documents and ideas
- (Optional) A public web URL so you can connect from other devices
- Claude Code can act as an agent layer on top of the vault, making recurring knowledge tasks reusable and faster
