---
publish: true
title: Quartz Publisher Skill Guide
created: 2026-04-14T17:37:39.622+03:00
modified: 2026-04-15T14:32:57.718+03:00
---

## What This Skill Does

The **quartz-publisher** skill takes your Obsidian vault and publishes it as a live public website using [Quartz](https://quartz.jzhao.xyz) and GitHub Pages — fully automated.

It handles everything: creating the GitHub repo, configuring the deploy pipeline, installing the sync plugin, and pushing all your notes live. You end up with a URL like `https://your-username.github.io/your-repo/`.

This also shows the deeper value of Claude Code: it can act like a specialized agent for a defined job. Instead of manually repeating a long publishing process, you use a reusable workflow that saves hours and can be improved over time.

---

## Prerequisites

- **GitHub CLI** (`gh`) is installed — run `gh --version` to check
- Obsidian is **open** with the vault you want to publish
- The **Local REST API** plugin is installed and running (run the [[Synced Vault Guild (MCP)]] first if not)

> [!Note]
> The skill will install the Local REST API plugin automatically if it's missing — but if you've already done the synced-vault setup, you're ready to go.

---

## Step 1 — Install the Skill

Attach the skill content to Claude Code, or copy it manually, using the prompt below.\
The skill file is here: [[Quartz Publisher Skill content]]

> [!Prompt]
> Install this skill globally "https://github.com/abdulrahman-alpha-apps/claude-pirates/blob/v4/content/Obsidian/Skills/Publish%20Vault/Quartz%20Publisher%20Skill%20content.md"

---

## Step 2 — Run the Skill

Open Obsidian → open the Terminal plugin → run Claude Code inside your vault directory, then trigger the skill with the repo name you want:

> [!Prompt]
> Run quartz-publisher skill. Use the repo name: my-garden

Replace `my-garden` with any lowercase-hyphenated name you want for your GitHub repo.

This is Claude Code working as an agent system, not just a chat tool. The process becomes a repeatable publishing agent that can be shared, reused, and adapted.

---

## What You'll Need to Do (Manual Steps)

The skill keeps user actions to the absolute minimum:

| # | Action | When |
|---|--------|------|
| 1 | Run `gh auth login` in terminal | Only if not already logged in to GitHub CLI |
| 2 | Run `gh auth refresh -s workflow` | Only if the `workflow` scope is missing from your token |
| 3 | **Restart Obsidian** once | Required after Local REST API plugin installs — so the API key is generated |

Everything else is automated.

---

## What You Get After Setup

- A live website at `https://<your-username>.github.io/<repo-name>/`
- All vault notes pushed to the site
- A GitHub repo with automatic redeploy on every push
- An ongoing sync command — just say **"sync"** to push new or updated notes
- A reusable publishing agent workflow that can be used again in future vaults

---

## Re-Syncing Later

After the initial setup, any time you add or update notes just say:

> [!Prompt]
> Sync
