---
publish: true
title: Claude Code Slash Commands
created: 2026-04-15T09:38:07.805+03:00
modified: 2026-04-15T09:38:07.806+03:00
---

# Claude Code Slash Commands

This note collects the most useful Claude Code slash commands in a more organized format. It is based on Anthropic's Claude Code docs plus a local cross-check against the installed `claude` CLI.

> [!Note]
> Slash commands can change by Claude Code version, account type, and enabled features. Think of this as a practical field guide for the current setup, not a forever-complete spec.

## Best Commands to Learn First

| Command | Use it for |
|---|---|
| `/help` | See available commands and usage |
| `/clear` | Start a fresh conversation |
| `/compact` | Shrink conversation context |
| `/init` | Create a `CLAUDE.md` project instruction file |
| `/memory` | Inspect or manage Claude memory |
| `/model` | Switch models |
| `/permissions` | Adjust tool access and approvals |
| `/status` | Check auth, session, and account state |
| `/mcp` | Configure MCP servers and connections |
| `/doctor` | Troubleshoot setup and environment problems |

## 1. Session and Context Commands

These are the commands you will probably use most often during normal work.

| Command | What it does | When to use it |
|---|---|---|
| `/clear` | Clears the current conversation | When changing topics completely |
| `/compact` | Compresses the thread to save context | When the chat is getting long |
| `/rewind` | Steps back or recovers conversation state | When the session goes off track |
| `/status` | Shows current session and auth info | When things feel inconsistent |
| `/exit` | Leaves the current Claude Code session | When you are done |

## 2. Project and Instruction Commands

These are especially relevant for this vault and other repositories.

| Command | What it does | When to use it |
|---|---|---|
| `/init` | Creates a `CLAUDE.md` file with project instructions | When starting in a new vault or repo |
| `/memory` | Views and manages saved Claude memory | When pruning stale instructions or checking remembered context |
| `/agents` | Shows or works with configured agents | When using role-specific workflows |

### Example

```text
/init
```

Use this at the start of a new project so Claude has a local instruction file to follow.

## 3. Model and Performance Commands

| Command | What it does | When to use it |
|---|---|---|
| `/model` | Switches to another model | When you want faster or deeper responses |
| `/compact` | Frees space in the context window | When you are nearing context limits |
| `/fast` | Enables a faster mode in supported setups | When speed matters more than depth |
| `/effort` | Adjusts reasoning effort in supported versions | When you want Claude to think harder or lighter |
| `/stats` | Shows session or performance info in supported builds | When monitoring usage or behavior |

### Example

```text
/model sonnet
```

## 4. Authentication and Account Commands

| Command | What it does | When to use it |
|---|---|---|
| `/login` | Signs in to Claude.ai-backed features | When cloud features or auth are required |
| `/logout` | Signs out and refreshes account state | When switching accounts or plans |
| `/status` | Confirms current account/session state | When login state seems wrong |
| `/extra-usage` | Enables additional usage in supported account types | When you need more capacity |

## 5. Permissions and Environment Commands

| Command | What it does | When to use it |
|---|---|---|
| `/permissions` | Reviews or changes what Claude can access | When tool access is blocked |
| `/terminal-setup` | Helps connect Claude to your terminal workflow | When configuring terminal integration |
| `/sandbox` | Shows sandbox/install/help info in supported builds | When environment restrictions are confusing |
| `/theme` | Changes display theme in supported builds | When adjusting the interface |

## 6. MCP and Integration Commands

These matter a lot for Obsidian workflows.

| Command | What it does | When to use it |
|---|---|---|
| `/mcp` | Manages MCP servers and connections | When wiring Claude to Obsidian or other tools |
| `/chrome` | Works with Claude in Chrome integration | When using browser-connected workflows |
| `/remote-control` | Connects or retries remote control features | When controlling a remote environment |

### Best fit for this vault

For **Claude Pirates**, the most relevant commands are:

- `/init`
- `/mcp`
- `/memory`
- `/compact`
- `/doctor`
- `/permissions`

## 7. Review, Feedback, and Collaboration Commands

| Command | What it does | When to use it |
|---|---|---|
| `/review` | Runs a local review flow when available | When checking current code changes |
| `/ultrareview` | Uses cloud-backed review in supported accounts | When you want a stronger review pass |
| `/feedback` | Sends product feedback | When reporting UX friction or ideas |
| `/issue` | Reports an issue or opens bug-reporting flow | When something is broken |
| `/bug` | Bug-report shortcut mentioned in docs/reference material | When documenting a bug quickly |

## 8. Plugin and Extension Commands

| Command | What it does | When to use it |
|---|---|---|
| `/plugin` | Manages plugins in supported versions | When working with Claude plugins |
| `/plugins` | Refreshes or lists plugin state in supported versions | When plugin state looks stale |
| `/reload-plugins` | Reloads plugin changes | After editing plugin config or installing plugins |

## 9. Quick Reference by Situation

| Situation | Best command |
|---|---|
| New project or vault | `/init` |
| Claude lost the plot | `/rewind` or `/clear` |
| Context is too full | `/compact` |
| Need to switch models | `/model` |
| Need Obsidian or tool integration | `/mcp` |
| Permissions are blocking work | `/permissions` |
| Login feels broken | `/status`, `/login`, `/logout` |
| Something is not working | `/doctor` |

## 10. My Recommended Starter Set

If you only memorize a small group, start with these:

1. `/init`
2. `/clear`
3. `/compact`
4. `/model`
5. `/memory`
6. `/permissions`
7. `/status`
8. `/mcp`
9. `/doctor`
10. `/login`

## Notes on Availability

- Some commands are universal, like `/clear`, `/compact`, `/init`, `/model`, `/memory`, `/mcp`, and `/status`
- Some are account- or feature-dependent, such as `/ultrareview`, `/chrome`, `/remote-control`, and `/extra-usage`
- Some appear in certain Claude Code builds even if you do not use them every day, such as `/plugin`, `/reload-plugins`, `/sandbox`, and `/theme`

## Sources

- Anthropic Claude Code docs: `https://docs.anthropic.com/en/docs/claude-code/slash-commands`
- Anthropic commands reference: `https://platform.claude.com/docs/en/commands`
- Local cross-check: installed `claude` CLI on this machine
