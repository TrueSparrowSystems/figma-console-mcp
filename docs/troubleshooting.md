---
title: "Troubleshooting"
description: "Solutions to common issues including browser connection, console logs, screenshots, and configuration problems."
---

# Troubleshooting Guide

## Common Issues and Solutions

### Plugin Debugging: Setup Workflow ✅

**For Plugin Developers in Local Mode:**

> **FIRST-TIME SETUP:**
>
> 1. Open Figma Desktop normally (no special flags needed)
> 2. Go to **Plugins → Development → Import plugin from manifest...**
> 3. Select `figma-sparrow-bridge/manifest.json` from the figma-sparrow-mcp directory
> 4. Run the plugin in your Figma file — it auto-connects via WebSocket
>
> ✅ **One-time import.** The plugin stays in your Development plugins list. Just run it each session.

### How to Verify Setup is Working

Before trying to get console logs, verify your setup:

```
"Check Figma status"
```

You should see something like:
```json
{
  "setup": {
    "valid": true,
    "message": "✅ Figma Desktop connected via WebSocket (Desktop Bridge Plugin)"
  }
}
```

If you see `"valid": false`, the AI will provide step-by-step setup instructions.

---

### WebSocket Bridge Troubleshooting

#### Plugin Shows "Disconnected"
**Cause:** MCP server is not running (it hosts the WebSocket server on ports 9223–9232).
**Fix:** Start or restart your MCP client (Claude Code, Cursor, etc.) so the MCP server process starts.

#### Plugin Not Appearing in Development Plugins
**Cause:** Plugin manifest not imported.
**Fix:** Go to Figma → Plugins → Development → Import plugin from manifest... → select `figma-sparrow-bridge/manifest.json`.

#### Port 9223 Already in Use
**Cause:** Another MCP server instance is running on port 9223 (common with Claude Desktop's dual-tab architecture).
**Fix (v1.10.0+):** The server now automatically falls back to the next available port in the range 9223–9232. No action needed on the server side. If the plugin can't connect to the fallback port, re-import the Sparrow Bridge manifest in Figma (see below).

#### Plugin Not Connecting After Port Fallback
**Cause:** The Sparrow Bridge plugin was imported before v1.10.0 and only scans port 9223. The server fell back to a different port.
**Fix:** Re-import the Sparrow Bridge plugin manifest in Figma:
1. Go to **Plugins → Development → Import plugin from manifest...**
2. Select `figma-sparrow-bridge/manifest.json` from the figma-sparrow-mcp package directory
3. Run the plugin — it will now scan ports 9223–9232 and connect to all active servers

> **Local Git users:** The manifest is in the `figma-sparrow-bridge/` directory of your cloned repository. Run `figma_get_status` — the AI will show you the exact path via the `pluginPath` field in the status output.

> **Why re-import?** Figma caches plugin files at the application level. Restarting the plugin does NOT reload code from disk. Re-importing the manifest is a one-time step that forces Figma to load the updated multi-port scanning code.

#### Running in Docker
**Cause:** The WebSocket server binds to `localhost` by default, which is unreachable from the Docker host.
**Fix:** Set `FIGMA_WS_HOST=0.0.0.0` in your container environment and expose the port with `-p 9223:9223`.

#### Plugin Connected but Commands Timeout
**Cause:** Plugin may be running in a different Figma file than expected.
**Fix:** The MCP server routes commands to the active file. Make sure the Desktop Bridge Plugin is running in the file you want to work with. Use `figma_get_status` to see which file is connected.

---

### The Simplest Workflow - No Navigation Needed!

Once setup is complete, just ask your AI to check console logs:

```
"Check the last 20 console logs"
```

Then run your plugin in Figma Desktop, and ask again:

```
"Check the last 20 console logs"
```

You'll see all your `[Main]`, `[Swapper]`, `[Serializer]`, etc. plugin logs immediately:

```json
{
  "logs": [
    {
      "timestamp": 1759747593482,
      "level": "log",
      "message": "[Main] ✓ Instance Swapping: 0 swapped, 20 unmatched",
      "source": "figma"
    },
    {
      "timestamp": 1759747593880,
      "level": "log",
      "message": "[Serializer] Collected 280 variables, 144 paint styles",
      "source": "figma"
    }
  ]
}
```

**That's it!** No navigation, no browser setup, no complex configuration.

---

## Workflow Best Practices

### Recommended Workflow

```
# 1. Check initial state
figma_get_status()

# 2. Work with plugin, then check logs
figma_get_console_logs({ level: 'error' })

# 3. Capture UI state
figma_take_screenshot({ target: 'plugin' })

# 4. Make code changes, reload
figma_reload_plugin({ clearConsole: true })

# 5. Clear for next test
figma_clear_console()
```

### Tips

**1. Use figma_get_status for Health Checks**
- Lightweight and fast
- Shows connection state
- Shows log count without retrieving logs

**2. Clear Console Between Tests**
- Prevents old logs from mixing with new ones
- `figma_clear_console()` or `figma_reload_plugin({ clearConsole: true })`

**3. Be Patient on First Connection**
- Initial WebSocket connection may take a moment
- First calls may be slower
- Subsequent operations are faster

**4. Check Error Messages**
- Error messages include helpful hints
- Often suggest the next step to try
- Include troubleshooting tips

---

## Getting Help

If you're still experiencing issues:

1. **Check Error Message Details**
   - Error messages include specific troubleshooting steps
   - Follow the hints provided

2. **Report Issues**
   - GitHub Issues: https://github.com/TrueSparrowSystems/figma-sparrow-mcp/issues
   - Include error messages
   - Include steps to reproduce
   - Include figma_get_status output

---

## Technical Details

### WebSocket Session Lifecycle

1. **MCP Server Starts:**
   - Launches WebSocket server on port 9223 (or 9224-9232 if port in use)
   - Waits for Desktop Bridge Plugin connection

2. **Plugin Connects:**
   - Scans ports 9223-9232 to find active server
   - Establishes persistent WebSocket connection
   - Begins real-time log capture

3. **Session Active:**
   - Commands execute via WebSocket
   - Logs streamed in real-time
   - Connection persists until plugin closed or Figma quits

### Console Log Buffer

- **Size:** 1000 logs (configurable)
- **Type:** Circular buffer (oldest logs dropped when full)
- **Capture:** Real-time via WebSocket (Desktop Bridge Plugin)
- **Source Detection:** Automatically identifies plugin vs Figma logs

### Screenshot Capture

- **Method:** Figma Plugin API `exportAsync()`
- **Format:** PNG base64-encoded
- **Scope:** Node-specific or full page captures

---

## Environment Variables

For local development or custom deployments:

```bash
# Log level (trace, debug, info, warn, error, fatal)
LOG_LEVEL=info

# Figma Personal Access Token
FIGMA_ACCESS_TOKEN=figd_YOUR_TOKEN_HERE

# WebSocket server host (default: localhost)
FIGMA_WS_HOST=0.0.0.0

# WebSocket server port (default: 9223)
FIGMA_WS_PORT=9223

# Enable MCP Apps
ENABLE_MCP_APPS=true
```

---

## Configuration

The MCP server uses sensible defaults that work for most use cases. For advanced configuration, see the [Setup Guide](/setup).
