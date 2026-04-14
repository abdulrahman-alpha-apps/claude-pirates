---
publish: true
title: Getting Started
created: 2026-04-14T17:37:27.563+03:00
modified: 2026-04-14T17:37:27.564+03:00
---

## Welcome

This vault is your hub for running **Claude Code** directly inside Obsidian. From here you can set up MCP integration, publish your vault to the web, and run AI-powered skills without leaving Obsidian.

---

## Step 1 — Install the Terminal Plugin

You need the Terminal plugin to run Claude Code inside Obsidian.

Open the link below and click **Install**, or search for "Terminal" in Obsidian's Community Plugins:

<https://obsidian.md/plugins?id=terminal>

![[Pasted image 20260414170934.png]]

> [!Note]
> After installing, make sure to **Enable** the plugin in Settings → Community Plugins.

---

## Step 2 — Open a Terminal Panel

Click the Terminal icon in the left sidebar to open a terminal panel inside Obsidian.

![[Pasted image 20260414171414.png]]

> [!Tip]
> For the best experience, open the terminal as a **split panel** alongside your notes so you can see both at once.

---

## Step 3 — Run Claude Code

In the terminal, navigate to your vault directory (if not already there), then start Claude:

> [!Code]
> claude

Claude Code will launch inside the terminal. You're now running AI directly inside Obsidian.

---

## What to Do Next

Once Claude is running, you can use the skills in this vault:

| Skill | What it does | Guide |
|-------|-------------|-------|
| **synced-vault** | Connect your vault to Claude via MCP — lets Claude read and write your notes | [[Synced Vault Guild]] |
| **quartz-publisher** | Publish your vault as a live website on GitHub Pages | [[Quartz Publisher Skill Guide]] |

> [!Tip]
> Start with **synced-vault** if you haven't set up MCP yet — quartz-publisher works best when the Local REST API plugin is already running.
