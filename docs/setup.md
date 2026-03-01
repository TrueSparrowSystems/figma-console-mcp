---
title: "Setup Guide"
description: "Complete setup instructions for connecting Figma Sparrow MCP to Claude Desktop, GitHub Copilot, Cursor, Windsurf, and other AI clients."
---

# Figma Sparrow MCP - Setup Guide

Complete setup instructions for connecting Figma Sparrow MCP to various AI clients including Claude Desktop, GitHub Copilot (VS Code), Cursor, Windsurf, and more.

---

## 🔧 Local Git Setup

**What you get:** All 56+ tools including design creation, variable management, component instantiation, and Sparrow Bridge Plugin support.

### Prerequisites

- [ ] Node.js 18+ installed
- [ ] Git installed
- [ ] Figma Desktop installed
- [ ] An MCP client installed (Claude Desktop, Claude Code, Cursor, Windsurf, etc.)

### Step 1: Clone and Build

```bash
# Clone the repository
git clone https://github.com/TrueSparrowSystems/figma-sparrow-mcp.git
cd figma-sparrow-mcp

# Install dependencies
npm install

# Build for local mode
npm run build:local
```

### Step 2: Get Your Figma Token (~2 min)

1. Go to [Manage personal access tokens](https://help.figma.com/hc/en-us/articles/8085703771159-Manage-personal-access-tokens) in Figma Help
2. Follow the steps to **create a new personal access token**
3. Enter description: `Figma Sparrow MCP`
4. Click **"Generate token"**
5. **Copy the token immediately** — you won't see it again!

> 💡 Your token starts with `figd_` — if it doesn't, something went wrong.

### Step 3: Configure Your MCP Client

#### Claude Code (CLI)

**Option A: CLI command (quickest)**

```bash
claude mcp add figma-sparrow -s user -e FIGMA_ACCESS_TOKEN=figd_YOUR_TOKEN_HERE -e ENABLE_MCP_APPS=true -- node /absolute/path/to/figma-sparrow-mcp/dist/local.js
```

**Option B: Edit the config file**

- **macOS / Linux:** `~/.claude.json`
- **Windows:** `%USERPROFILE%\.claude.json`

```json
{
  "mcpServers": {
    "figma-sparrow": {
      "command": "node",
      "args": ["/absolute/path/to/figma-sparrow-mcp/dist/local.js"],
      "env": {
        "FIGMA_ACCESS_TOKEN": "figd_YOUR_TOKEN_HERE",
        "ENABLE_MCP_APPS": "true"
      }
    }
  }
}
```

#### Other MCP Clients (Cursor, Windsurf, Claude Desktop, etc.)

Edit your client's MCP config file:

```json
{
  "mcpServers": {
    "figma-sparrow": {
      "command": "node",
      "args": ["/absolute/path/to/figma-sparrow-mcp/dist/local.js"],
      "env": {
        "FIGMA_ACCESS_TOKEN": "figd_YOUR_TOKEN_HERE",
        "ENABLE_MCP_APPS": "true"
      }
    }
  }
}
```

**Important:**
- Replace `/absolute/path/to/figma-sparrow-mcp` with the actual path where you cloned the repo
- Use forward slashes `/` even on Windows

### Step 4: Connect to Figma Desktop (~2 min)

#### Install the Sparrow Bridge Plugin

The Sparrow Bridge Plugin connects via WebSocket — no special Figma launch flags needed, and it persists across Figma restarts.

1. **Open Figma Desktop** (normal launch, no special flags)
2. Go to **Plugins** → **Development** → **Import plugin from manifest...**
3. Navigate to the `figma-sparrow-bridge/manifest.json` file in the figma-sparrow-mcp directory
4. Click **"Open"** — the plugin appears in your Development plugins list
5. **Run the plugin** in your Figma file (Plugins → Development → Figma Sparrow Bridge)
6. The plugin auto-connects via WebSocket (scans ports 9223–9232) — you'll see a "Connected" indicator

> **One-time setup.** Once imported, the plugin stays in your Development plugins list. Just run it whenever you want to use the MCP. No need to restart Figma with special flags.

**📖 [Sparrow Bridge Plugin Documentation](https://github.com/TrueSparrowSystems/figma-sparrow-mcp/tree/main/figma-sparrow-bridge)**

#### Multi-Instance / Port Conflicts

If you're running multiple MCP clients (e.g., Claude Desktop Chat + Code tabs, or Claude + Cursor simultaneously), the server automatically handles port conflicts:

1. **Update** to the latest version and restart your MCP client(s)
2. **Re-import the Sparrow Bridge Plugin** in Figma (Plugins → Development → Import plugin from manifest). This is the critical step — the updated plugin scans ports 9223–9232 instead of only 9223
3. **Run the plugin** in your Figma file — it will find whichever port each server landed on

Step 2 is a one-time update. After re-importing, the plugin automatically connects to all active server instances across the full port range.

### Step 5: Restart Your MCP Client (~1 min)

1. **Restart your MCP client** (quit and reopen Claude Code, Cursor, Windsurf, Claude Desktop, etc.)
2. Verify the MCP server is connected (e.g., in Claude Desktop look for the 🔌 icon showing "figma-sparrow: connected")

### Step 6: Test It! (~2 min)

Try these prompts to verify everything works:

```
Check Figma status
```
→ Should show connection status with active WebSocket transport

```
Search for button components
```
→ Should return component results from your open Figma file

```
Create a simple frame with a blue background
```
→ Should create a frame in your Figma file (this confirms write access!)

**🎉 You're all set!** You now have full AI-assisted design capabilities.

### Updating

To get the latest changes:

```bash
cd figma-sparrow-mcp
git pull
npm install
npm run build:local
```

Then restart your MCP client.

---

## 🛠️ Troubleshooting

### Quick Fixes

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| "Failed to connect to Figma Desktop" | No transport available | Install Sparrow Bridge Plugin and run it in your Figma file |
| "FIGMA_ACCESS_TOKEN not configured" | Missing or wrong token | Check token in config, must start with `figd_` |
| "Command not found: node" | Node.js not installed | Install Node.js 18+ from nodejs.org |
| Tools not appearing in MCP client | Config not loaded | Restart your MCP client completely |
| "Port 9223 already in use" | Another MCP instance running | As of v1.10.0, the server automatically falls back to ports 9224–9232. If the plugin can't connect, re-import the Desktop Bridge manifest. |
| WebSocket unreachable from Docker host | Server bound to localhost | Set `FIGMA_WS_HOST=0.0.0.0` and expose port with `-p 9223:9223` |
| Plugin shows "Disconnected" | MCP server not running | Start/restart your MCP client so the server starts |

### Node.js Version Issues

**Symptom:** Cryptic errors like "parseArgs not exported from 'node:util'"

**Fix:** You need Node.js 18 or higher.

```bash
# Check your version
node --version

# Should show v18.x.x or higher
```

If using **NVM** and having issues, try using the absolute path to Node:

```json
{
  "mcpServers": {
    "figma-sparrow": {
      "command": "/Users/yourname/.nvm/versions/node/v20.10.0/bin/node",
      "args": ["-e", "require('figma-sparrow-mcp')"],
      "env": {
        "FIGMA_ACCESS_TOKEN": "figd_YOUR_TOKEN_HERE"
      }
    }
  }
}
```

### Config File Syntax Errors

If Claude Desktop doesn't see your MCP server:

1. **Validate your JSON:** Use a tool like [jsonlint.com](https://jsonlint.com)
2. **Check for common mistakes:**
   - Missing commas between properties
   - Trailing commas (not allowed in JSON)
   - Wrong quote characters (must be `"` not `'` or smart quotes)
3. **Copy the exact config** from this guide — don't retype it

### Still Having Issues?

1. Check the [GitHub Issues](https://github.com/TrueSparrowSystems/figma-sparrow-mcp/issues)
2. Ask in [Discussions](https://github.com/TrueSparrowSystems/figma-sparrow-mcp/discussions)
3. Include:
   - The exact error message
   - Output of `node --version`
   - Your MCP client (Claude Desktop, Claude Code, etc.)

---

## Optional: Enable MCP Apps

MCP Apps provide interactive UI experiences like the Token Browser and Design System Dashboard. As of v1.10.0, `ENABLE_MCP_APPS=true` is included in the default configuration examples above.

If you set up before v1.10.0, add `"ENABLE_MCP_APPS": "true"` to the `env` section of your MCP config.

> **Note:** MCP Apps require a client with [ext-apps protocol](https://github.com/anthropics/anthropic-cookbook/tree/main/misc/model_context_protocol/ext-apps) support.

---

## Next Steps

1. **Try example prompts:** See [Use Cases](use-cases) for workflow examples
2. **Explore all tools:** See [Tools Reference](tools) for the complete tool list
3. **Learn about the Sparrow Bridge Plugin:** See [Desktop Bridge README](https://github.com/TrueSparrowSystems/figma-sparrow-mcp/tree/main/figma-sparrow-bridge) for advanced configuration

---

## Support

- 📖 [Full Documentation](/)
- 🐛 [Report Issues](https://github.com/TrueSparrowSystems/figma-sparrow-mcp/issues)
- 💬 [Discussions](https://github.com/TrueSparrowSystems/figma-sparrow-mcp/discussions)
