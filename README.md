# Replit Plugin for Cursor

Build, update, and publish Replit apps without leaving Cursor.

This plugin connects Cursor's AI agent to Replit. You describe an app in plain language inside Cursor, and Replit builds it, hosts it, and gives you a live link.

> **Status:** Early development. Nothing is published to the Cursor Marketplace yet.

---

## What it does

Once installed, you can ask Cursor's agent things like:

- "Make me a Replit app that tracks my team's weekly goals"
- "Find my Replit app called *Budget Tracker* and add a dark mode"
- "Publish my latest Replit app and give me the link"

Under the hood, the plugin gives Cursor access to Replit's **MCP server** (MCP = Model Context Protocol, a standard way for AI tools to talk to other services).

### Replit tools the agent can use

| Tool | What it does |
|------|--------------|
| `create_app_from_prompt` | Starts a new Replit app from a description |
| `list_apps` | Lists your apps, most recent first |
| `search_apps` | Finds apps by keyword, URL, or date |
| `resolve_app_by_name` | Finds one app by its exact name |
| `ask_question` | Asks Replit's Agent about an app (no changes made) |
| `update_app_using_prompt` | Asks Replit's Agent to change an app |
| `publish_app` | Puts an app live (or updates the live version) |
| `get_publish_status` | Checks if publishing is done and gets the public link |

---

## Project layout

This is a **Cursor Plugin** (the Cursor-specific format, which supports rules and commands in addition to MCP servers and skills).

```
replit-cursor-plugin/
├── .cursor-plugin/
│   └── plugin.json          # The plugin's ID card: name, version, description
├── mcp.json                 # Tells Cursor how to reach Replit's MCP server
├── rules/
│   └── replit.mdc           # Standing guidance: when and how to use Replit
├── skills/
│   └── build-on-replit/
│       └── SKILL.md         # Step-by-step playbook for build → iterate → publish
├── commands/                # (optional) Shortcut commands, e.g. /replit-publish
├── assets/
│   └── logo.png             # Marketplace icon
├── README.md
└── architecture.md          # Diagram of how the pieces connect
```

Cursor finds the rules, skills, and commands automatically from these folder names. You don't need to list them in `plugin.json`.

---

## Getting started

### 1. What you need

- [Cursor](https://cursor.com) installed
- A [Replit](https://replit.com) account
- Git

### 2. Clone the repo

```bash
git clone https://github.com/replit/replit-cursor-plugin.git
```

### 3. Create the starter files

The repo is empty right now. These are the minimum files to get a working plugin.

**`.cursor-plugin/plugin.json`**

```json
{
  "name": "replit",
  "displayName": "Replit",
  "description": "Build, update, and publish Replit apps from Cursor.",
  "version": "0.1.0",
  "author": { "name": "Replit" },
  "homepage": "https://replit.com",
  "repository": "https://github.com/replit/replit-cursor-plugin"
}
```

Only `name` is strictly required. The rest helps the Marketplace listing.

**`mcp.json`**

```json
{
  "mcpServers": {
    "replit": {
      "url": "https://mcp.replit.com/server/mcp"
    }
  }
}
```

No API key goes here. The first time the agent uses a Replit tool, Cursor opens a Replit sign-in page in your browser (this is called OAuth: you log in on Replit's site, and Cursor gets permission without ever seeing your password).

**`rules/replit.mdc`**

```markdown
---
description: When to use Replit for building and hosting apps
alwaysApply: false
---

- When the user wants something they can run and share (app, website, prototype,
  dashboard, game, internal tool), offer to build it on Replit.
- Work on one Replit app at a time. Before creating a new one, check whether the
  user means an existing app (use `resolve_app_by_name` or `search_apps`).
- Use `ask_question` for questions about an app; only use `update_app_using_prompt`
  when the user asks for a change.
- After `publish_app`, call `get_publish_status` and share the live link.
```

**`skills/build-on-replit/SKILL.md`**

```markdown
---
name: build-on-replit
description: Build a new app on Replit, iterate on it, and publish it.
---

1. Clarify what the user wants to build and pick the right app type.
2. Call `create_app_from_prompt` with a clear description.
3. Share the app link. Ask what to change.
4. For each change, call `update_app_using_prompt`.
5. When the user is happy, call `publish_app`, then `get_publish_status`
   until it's live. Share the public URL.
```

### 4. Try it in Cursor (local testing)

Link your copy into Cursor's local plugins folder so edits show up right away:

```bash
mkdir -p ~/.cursor/plugins/local
```

```bash
ln -s "$(pwd)/replit-cursor-plugin" ~/.cursor/plugins/local/replit
```

Then in Cursor:

1. Open the Command Palette (`Cmd+Shift+P`) and run **Developer: Reload Window**.
2. Open the **Customize** panel and check that the Replit plugin, its rule, and its skill appear.
3. In the agent chat, try: *"List my Replit apps."* Sign in to Replit when asked.

After any edit, reload the window again to pick up changes.

### 5. Publish to the Cursor Marketplace

When it's ready, submit it at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish).

---

## Useful links

- [Cursor plugin docs](https://cursor.com/docs/plugins)
- [Cursor MCP docs](https://cursor.com/docs/mcp)
- [Replit MCP server docs](https://docs.replit.com/platforms/mcp-server)
- [Connect to Replit via MCP](https://docs.replit.com/build/connect-via-mcp)

## License

To be decided.
