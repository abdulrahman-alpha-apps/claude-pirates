---
publish: true
description: Publish an Obsidian vault to GitHub Pages using Quartz. Use this skill whenever the user wants to publish their vault, create a public website from Obsidian notes, set up Quartz, deploy to GitHub Pages, or sync vault notes to a live site. Trigger on phrases like "publish my vault", "quartz", "github pages", "publish notes", or "make my vault public".
created: 2026-04-14T17:34:05.088+03:00
modified: 2026-04-15T14:16:27.190+03:00
---

# Quartz Publisher — Obsidian Vault Publishing

This skill publishes the user's Obsidian vault to a public website using Quartz and GitHub Pages. Handle every step programmatically. Only ask the user to act when it is physically impossible to automate (browser OAuth, Obsidian restart). Keep user actions to the absolute minimum.

---

## What You Will Do

1. Verify GitHub CLI auth
2. Create a GitHub repo from the Quartz template
3. Enable GitHub Pages
4. Add the deploy workflow
5. Install Local REST API plugin via CLI
6. Configure Quartz Syncer plugin
7. Fix Quartz config (baseUrl, title)
8. Create homepage
9. Sync all vault notes

---

## Step 1 — Check GitHub Auth

```bash
gh auth status
```

If not logged in, tell the user:

> "Please run `gh auth login` in your terminal and follow the browser prompt, then come back."

Wait for confirmation before continuing.

If logged in, check for `workflow` scope in the token scopes list.

If `workflow` scope is missing, tell the user:

> "Please run `gh auth refresh -s workflow` in your terminal and approve it in the browser, then come back."

Wait for confirmation, then verify with `gh auth status` that `workflow` appears in scopes.

---

## Step 2 — Create GitHub Repo

Ask the user for a repo name if they haven't provided one. Use lowercase with hyphens (e.g. `my-garden`).

```bash
gh repo create <username>/<repo-name> --public --template jackyzha0/quartz --description "<repo-name> - Obsidian vault published with Quartz"
```

Then enable GitHub Pages with workflow deployment:

```bash
gh api repos/<username>/<repo-name>/pages -X POST --field build_type=workflow
```

Store `<username>` and `<repo-name>` for use in later steps.

---

## Step 3 — Add Deploy Workflow

Clone the repo:

```bash
gh repo clone <username>/<repo-name> /tmp/<repo-name>
```

Create `.github/workflows/deploy.yaml`:

```yaml
name: Deploy Quartz site to GitHub Pages

on:
  push:
    branches:
      - v4
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: actions/setup-node@v4
        with:
          node-version: 22
      - name: Install Dependencies
        run: npm ci
      - name: Build Quartz
        run: npx quartz build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: public

  deploy:
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

Commit and push:

```bash
cd /tmp/<repo-name>
git add .github/workflows/deploy.yaml
git commit -m "Add GitHub Pages deploy workflow"
git push origin v4
```

---

## Step 4 — Install Local REST API Plugin via CLI

Detect the vault path from the current working directory (or ask the user if unclear):

```bash
VAULT_PATH="$(pwd)"
```

Get the latest release of the Local REST API plugin:

```bash
LATEST=$(curl -s https://api.github.com/repos/coddingtonbear/obsidian-local-rest-api/releases/latest | python3 -c "import sys,json; print(json.load(sys.stdin)['tag_name'])")
```

Create the plugin directory and download files:

```bash
mkdir -p "$VAULT_PATH/.obsidian/plugins/obsidian-local-rest-api"

curl -sL "https://github.com/coddingtonbear/obsidian-local-rest-api/releases/download/$LATEST/main.js" \
  -o "$VAULT_PATH/.obsidian/plugins/obsidian-local-rest-api/main.js"

curl -sL "https://github.com/coddingtonbear/obsidian-local-rest-api/releases/download/$LATEST/manifest.json" \
  -o "$VAULT_PATH/.obsidian/plugins/obsidian-local-rest-api/manifest.json"

curl -sL "https://github.com/coddingtonbear/obsidian-local-rest-api/releases/download/$LATEST/styles.css" \
  -o "$VAULT_PATH/.obsidian/plugins/obsidian-local-rest-api/styles.css" 2>/dev/null || true
```

Enable the plugin by adding it to `community-plugins.json`:

```bash
python3 - <<'EOF'
import json, os

path = os.path.join(os.environ['VAULT_PATH'], '.obsidian', 'community-plugins.json')
plugins = json.load(open(path)) if os.path.exists(path) else []
if 'obsidian-local-rest-api' not in plugins:
    plugins.append('obsidian-local-rest-api')
    json.dump(plugins, open(path, 'w'), indent=2)
    print("Plugin enabled.")
else:
    print("Plugin already enabled.")
EOF
```

Tell the user:

> "The Local REST API plugin has been installed. Please **restart Obsidian** now (quit and reopen it), then come back."

After the user confirms Obsidian has restarted, read the API key:

```bash
API_KEY=$(python3 -c "import json; d=json.load(open('$VAULT_PATH/.obsidian/plugins/obsidian-local-rest-api/data.json')); print(d['apiKey'])")
```

Verify it works:

```bash
curl -sk -H "Authorization: Bearer $API_KEY" https://127.0.0.1:27124/
```

Expected: `"authenticated": true`. If not, ask the user to restart Obsidian again and retry.

---

## Step 5 — Configure Quartz Syncer Plugin

Check if Quartz Syncer is installed:

```bash
ls "$VAULT_PATH/.obsidian/plugins/quartz-syncer/data.json" 2>/dev/null
```

If not found, install it the same way as Step 4 using releases from `https://github.com/saberzero1/quartz-syncer/releases/latest`.

Once confirmed installed, extract the GitHub token from the CLI:

```bash
GH_TOKEN=$(gh auth token)
GH_USER=$(gh api user -q .login)
```

Write the full configuration to `data.json`:

```bash
python3 - <<EOF
import json

path = "$VAULT_PATH/.obsidian/plugins/quartz-syncer/data.json"
with open(path) as f:
    data = json.load(f)

data['git']['remoteUrl'] = 'https://github.com/$GH_USER/$REPO_NAME'
data['git']['branch'] = 'v4'
data['git']['providerHint'] = 'github'
data['git']['auth'] = {
    'type': 'basic',
    'username': '$GH_USER',
    'password': '$GH_TOKEN'
}

with open(path, 'w') as f:
    json.dump(data, f, indent=2)

print("Quartz Syncer configured.")
EOF
```

---

## Step 6 — Fix Quartz Config

Update `quartz.config.ts` in the cloned repo:

```bash
cd /tmp/<repo-name>

python3 - <<EOF
content = open('quartz.config.ts').read()
content = content.replace('quartz.jzhao.xyz', '$GH_USER.github.io/$REPO_NAME')
content = content.replace('"Quartz 4"', '"$SITE_TITLE"')
open('quartz.config.ts', 'w').write(content)
print("Config updated.")
EOF
```

Use the repo name (title-cased) as the site title if the user hasn't specified one.

Commit and push:

```bash
git add quartz.config.ts
git commit -m "Set correct baseUrl and site title"
git push origin v4
```

---

## Step 7 — Create Homepage

Create `content/index.md` in the cloned repo:

```bash
cat > /tmp/<repo-name>/content/index.md << 'EOF'
---
title: <Site Title>
---

Welcome to <Site Title>.

## Notes

EOF
```

---

## Step 8 — Sync Vault Notes

List all markdown files in the vault using the Local REST API:

```bash
curl -sk -H "Authorization: Bearer $API_KEY" https://127.0.0.1:27124/vault/
```

For each `.md` file returned:

1. Read its content via `GET /vault/<filename>`
2. Skip if empty
3. Generate a slug (lowercase, spaces to hyphens, strip special chars)
4. Write to `/tmp/<repo-name>/content/<slug>.md` with frontmatter:

```markdown
---
title: <Original filename without .md>
publish: true
---

<content>
```

5. Add a link to `content/index.md` for each note

After processing all files, commit and push everything:

```bash
cd /tmp/<repo-name>
git add content/
git commit -m "Sync vault notes"
git push origin v4
```

Check the deploy:

```bash
gh run list --repo <username>/<repo-name> --workflow=deploy.yaml --limit 1
```

---

## Step 9 — Confirm Live

Once the deploy shows `completed success`, tell the user:

> "Your vault is live at: https://<username>.github.io/<repo-name>/"
>
> Any time you add or update notes, just say **'sync'** and I'll push the changes automatically."

---

## User Actions Summary (Minimal)

| # | Action | Why |
|---|--------|-----|
| 1 | Run `gh auth login` (if needed) | Browser OAuth — cannot be automated |
| 2 | Run `gh auth refresh -s workflow` (if needed) | Requires browser approval |
| 3 | Restart Obsidian after plugin install | Obsidian must load the plugin to generate its API key |

Everything else is handled automatically.

---

## Re-Sync Command

When the user says "sync", "republish", or "new files":

1. Call `GET /vault/` to list all vault files
2. Read each `.md` file
3. Write updated content to `/tmp/<repo-name>/content/`
4. Update `content/index.md` with any new notes
5. `git add`, `git commit`, `git push`
6. Report what was added/updated
