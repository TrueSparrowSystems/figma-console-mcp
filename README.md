# Figma Sparrow MCP Server

[![MCP](https://img.shields.io/badge/MCP-Compatible-blue)](https://modelcontextprotocol.io/)

> **Your design system as an API.** Model Context Protocol server that bridges design and development—giving AI assistants complete access to Figma for **extraction**, **creation**, and **debugging**.

> **🆕 v1.11.1 — Doc Generator Fixes:** Cleaner `figma_generate_component_doc` output—proper markdown tables, filtered property metadata, Storybook links.

## What is this?

Figma Sparrow MCP connects AI assistants (like Claude) to Figma, enabling:

- **🐛 Plugin debugging** - Capture console logs, errors, and stack traces
- **📸 Visual debugging** - Take screenshots for context
- **🎨 Design system extraction** - Pull variables, components, and styles
- **✏️ Design creation** - Create UI components, frames, and layouts directly in Figma
- **🔧 Variable management** - Create, update, rename, and delete design tokens
- **⚡ Real-time monitoring** - Watch logs as plugins execute
- **🔄 Local Git install** - Clone the repo and run locally for full control

---

## ⚡ Quick Start

### For Contributors: Local Git Mode

**Best for:** Developers who want to modify source code or contribute to the project.

**What you get:** Full toolset plus source code access.

#### Quick Setup

```bash
# Clone and build
git clone https://github.com/TrueSparrowSystems/figma-sparrow-mcp.git
cd figma-sparrow-mcp
npm install
npm run build:local
```

#### Configure Your MCP Client

Add to your config file:

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

**📖 [Complete Setup Guide](docs/setup.md)**

---

## 🎯 Test Your Connection

After setup, try these prompts:

**Basic test (local mode):**
```
Navigate to https://www.figma.com and check status
```

**Design system test (requires auth):**
```
Get design variables from [your Figma file URL]
```

**Plugin test (Local Mode only):**
```
Show me the primary font for [your theme name]
```

---

## 🔐 Authentication

### Local Mode - Personal Access Token (Manual)

1. Visit https://help.figma.com/hc/en-us/articles/8085703771159-Manage-personal-access-tokens
2. Generate token
3. Add to MCP config as `FIGMA_ACCESS_TOKEN` environment variable

---

## 🛠️ Available Tools

### Navigation & Status
- `figma_navigate` - Open Figma URLs
- `figma_get_status` - Check connection status

### Console Debugging
- `figma_get_console_logs` - Retrieve console logs
- `figma_watch_console` - Real-time log streaming
- `figma_clear_console` - Clear log buffer
- `figma_reload_plugin` - Reload current page

### Visual Debugging
- `figma_take_screenshot` - Capture UI screenshots

### Design System Extraction
- `figma_get_variables` - Extract design tokens/variables
- `figma_get_component` - Get component data (metadata or reconstruction spec)
- `figma_get_component_for_development` - Component + image
- `figma_get_component_image` - Just the image
- `figma_get_styles` - Color, text, effect styles
- `figma_get_file_data` - Full file structure
- `figma_get_file_for_plugin` - Optimized file data

### ✏️ Design Creation (Local Mode + Sparrow Bridge)
- `figma_execute` - **Power tool**: Run any Figma Plugin API code to create designs
  - Create frames, shapes, text, components
  - Apply auto-layout, styles, effects
  - Build complete UI mockups programmatically
- `figma_arrange_component_set` - **Organize variants into professional component sets**
  - Convert multiple component variants into a proper Figma component set
  - Applies native purple dashed border visualization automatically
  - Creates white container frame with title, row labels, and column headers
  - Row labels vertically centered with each grid row
  - Column headers horizontally centered with each column
  - Use natural language like "arrange these variants" or "organize as component set"
- `figma_set_description` - **Document components with rich descriptions**
  - Add descriptions to components, component sets, and styles
  - Supports markdown formatting for rich documentation
  - Descriptions appear in Dev Mode for developers

### 🔍 Design-Code Parity (All Modes)
- `figma_check_design_parity` - Compare Figma component specs against code implementation, producing a scored diff report with actionable fix items
- `figma_generate_component_doc` - Generate platform-agnostic markdown documentation by merging Figma design data with code-side info

### 🔧 Variable Management (Local Mode + Sparrow Bridge)
- `figma_create_variable_collection` - Create new variable collections with modes
- `figma_create_variable` - Create COLOR, FLOAT, STRING, or BOOLEAN variables
- `figma_update_variable` - Update variable values in specific modes
- `figma_rename_variable` - Rename variables while preserving values
- `figma_delete_variable` - Delete variables
- `figma_delete_variable_collection` - Delete collections and all their variables
- `figma_add_mode` - Add modes to collections (e.g., "Dark", "Mobile")
- `figma_rename_mode` - Rename existing modes
- `figma_batch_create_variables` - Create up to 100 variables in one call (10-50x faster)
- `figma_batch_update_variables` - Update up to 100 variable values in one call
- `figma_setup_design_tokens` - Create complete token system (collection + modes + variables) atomically

**📖 [Detailed Tool Documentation](docs/TOOLS.md)**

---

## 📖 Example Prompts

### Plugin Debugging
```
Navigate to my Figma plugin and show me any console errors
Watch the console for 30 seconds while I test my plugin
Get the last 20 console logs
```

### Design System Extraction
```
Get all design variables from https://figma.com/design/abc123
Extract color styles and show me the CSS exports
Get the Button component with a visual reference image
Get the Badge component in reconstruction format for programmatic creation
```

### Design Creation (Local Mode)
```
Create a success notification card with a checkmark icon and message
Design a button component with hover and disabled states
Build a navigation bar with logo, menu items, and user avatar
Create a modal dialog with header, content area, and action buttons
Arrange these button variants into a component set
Organize my icon variants as a proper component set with the purple border
```

### Variable Management (Local Mode)
```
Create a new color collection called "Brand Colors" with Light and Dark modes
Add a primary color variable with value #3B82F6 for Light and #60A5FA for Dark
Rename the "Default" mode to "Light Theme"
Add a "High Contrast" mode to the existing collection
```

### Design-Code Parity
```
Compare the Button component in Figma against our React implementation
Check design parity for the Card component before sign-off
Generate component documentation for the Dialog from our design system
```

### Visual Debugging
```
Take a screenshot of the current Figma canvas
Navigate to this file and capture what's on screen
```

**📖 [More Use Cases & Examples](docs/USE_CASES.md)**

---

## 🎨 AI-Assisted Design Creation

> **⚠️ Local Mode Only:** This feature requires the Sparrow Bridge plugin and only works with Local Git installation.

One of the most powerful capabilities of this MCP server is the ability to **design complete UI components and pages directly in Figma through natural language conversation** with any MCP-compatible AI assistant like Claude Desktop or Claude Code.

### What's Possible

**Create original designs from scratch:**
```
Design a login card with email and password fields, a "Forgot password?" link,
and a primary Sign In button. Use 32px padding, 16px border radius, and subtle shadow.
```

**Leverage existing component libraries:**
```
Build a dashboard header using the Avatar component for the user profile,
Button components for actions, and Badge components for notifications.
```

**Generate complete page layouts:**
```
Create a settings page with a sidebar navigation, a main content area with form fields,
and a sticky footer with Save and Cancel buttons.
```

### How It Works

1. **You describe what you want** in plain English
2. **The AI searches your component library** using `figma_search_components` to find relevant building blocks
3. **Components are instantiated** with proper variants and properties via `figma_instantiate_component`
4. **Custom elements are created** using the full Figma Plugin API via `figma_execute`
5. **Visual validation** automatically captures screenshots and iterates until the design looks right

### Who Benefits

| Role | Use Case |
|------|----------|
| **Designers** | Rapidly prototype ideas without manual frame-by-frame construction. Explore variations quickly by describing changes. |
| **Developers** | Generate UI mockups during planning discussions. Create visual specs without switching to design tools. |
| **Product Managers** | Sketch out feature concepts during ideation. Communicate visual requirements directly to stakeholders. |
| **Design System Teams** | Test component flexibility by generating compositions. Identify gaps in component coverage. |
| **Agencies** | Speed up initial concept delivery. Iterate on client feedback in real-time during calls. |

### Example Workflows

**Brand New Design:**
> "Create a notification toast with an icon on the left, title and description text, and a dismiss button. Use our brand colors."

The AI creates custom frames, applies your design tokens, and builds the component from scratch.

**Component Composition:**
> "Build a user profile card using the Avatar component (large size), two Button components (Edit Profile and Settings), and a Badge for the user's status."

The AI searches your library, finds the exact components, and assembles them with proper spacing and alignment.

**Design Iteration:**
> "The spacing feels too tight. Increase the gap between sections to 24px and make the heading larger."

The AI modifies the existing design, takes a screenshot to verify, and continues iterating until you're satisfied.

### Visual Validation

The AI automatically follows a validation workflow after creating designs:

1. **Create** → Execute the design code
2. **Screenshot** → Capture the result
3. **Analyze** → Check alignment, spacing, and visual balance
4. **Iterate** → Fix any issues detected
5. **Verify** → Final screenshot to confirm

This ensures designs aren't just technically correct—they *look* right.

---

## 🎨 Sparrow Bridge Plugin (Recommended Connection)

The **Figma Sparrow Bridge** plugin is the recommended way to connect Figma to the MCP server. It communicates via WebSocket — no special Figma launch flags needed, and it persists across Figma restarts.

### Setup

1. Open Figma Desktop (normal launch — no debug flags needed)
2. Go to **Plugins → Development → Import plugin from manifest...**
3. Select `figma-sparrow-bridge/manifest.json` from the figma-sparrow-mcp directory
4. Run the plugin in your Figma file — it auto-connects via WebSocket (scans ports 9223–9232)
5. Ask your AI: "Check Figma status" to verify the connection

> **One-time import.** Once imported, the plugin stays in your Development plugins list. Just run it whenever you want to use the MCP.

**📖 [Sparrow Bridge Documentation](figma-sparrow-bridge/README.md)**

### Capabilities

**Read Operations:**
- Variables without Enterprise API
- Reliable component descriptions (bypasses API bugs)
- Multi-mode support (Light/Dark/Brand variants)
- Real-time selection tracking and document change monitoring

**Write Operations:**
- **Design Creation** - Create frames, shapes, text, components via `figma_execute`
- **Variable Management** - Full CRUD operations on variables and collections
- **Mode Management** - Add and rename modes for multi-theme support

### How the Transport Works

- The MCP server communicates via **WebSocket** through the Sparrow Bridge plugin
- The server tries port 9223 first, then automatically falls back through ports 9224–9232 if needed
- The plugin scans all ports in the range and connects to every active server it finds
- All 56+ tools work through the WebSocket transport

**Multiple files:** The WebSocket server supports multiple simultaneous plugin connections — one per open Figma file. Each connection is tracked by file key with independent state (selection, document changes, console logs).

**Environment variables:**
- `FIGMA_WS_PORT` — Override the preferred WebSocket port (default: 9223). The server will fall back through a 10-port range starting from this value if the preferred port is occupied.
- `FIGMA_WS_HOST` — Override the WebSocket server bind address (default: `localhost`). Set to `0.0.0.0` when running inside Docker so the host machine can reach the MCP server.

**Plugin Limitation:** Only works in Local Git mode.

---

## 🔀 Multi-Instance Support (v1.10.0)

Figma Sparrow MCP now supports **multiple simultaneous instances** — perfect for designers and developers who work across multiple projects or use Claude Desktop's Chat and Code tabs at the same time.

### The Problem (Before v1.10.0)

When two processes tried to start the MCP server (e.g., Claude Desktop's Chat tab and Code tab), the second one would crash with `EADDRINUSE` because both competed for port 9223.

### How It Works Now

- The server tries port **9223** first (the default)
- If that port is already taken, it automatically tries **9224**, then **9225**, and so on up to **9232**
- The Sparrow Bridge plugin in Figma connects to **all** active servers simultaneously
- Every server instance receives real-time events (selection changes, document changes, console logs)
- `figma_get_status` shows which port you're on and lists other active instances

### What This Means for You

| Scenario | Before v1.10.0 | Now |
|----------|----------------|-----|
| Two Claude Desktop tabs (Chat + Code) | Second tab crashes | Both work independently |
| Multiple CLI terminals on different projects | Only one can run | All run simultaneously |
| Claude Desktop + Claude Code CLI | Port conflict | Both coexist |

### Do I Need to Do Anything?

**If you're running a single instance:** Nothing changes. You'll still use port 9223 as before.

**If you want multi-instance:** Re-import the Sparrow Bridge plugin in Figma (Plugins → Development → Import plugin from manifest). This updates the plugin to scan all ports and connect to every active server.

> **Note:** The server-side update happens automatically when you update the npm package. Only the plugin needs a one-time re-import to enable multi-connection support.

---

## 🧩 MCP Apps (Experimental)

Figma Sparrow MCP includes support for **MCP Apps** — rich interactive UI experiences that render directly inside any MCP client that supports the [MCP Apps protocol extension](https://github.com/anthropics/anthropic-cookbook/tree/main/misc/model_context_protocol/ext-apps). Built with the official [`@modelcontextprotocol/ext-apps`](https://www.npmjs.com/package/@modelcontextprotocol/ext-apps) SDK.

> **What are MCP Apps?** Traditional MCP tools return text or images to the AI. MCP Apps go further — they render interactive HTML interfaces inline in the chat, allowing users to browse, filter, and interact with data directly without consuming AI context.

### Token Browser

An interactive design token explorer.

**Usage:** Ask Claude to "browse the design tokens" or "show me the design tokens" while connected to a Figma file.

**Features:**
- Browse all tokens organized by collection with expandable sections
- Filter by type (Colors, Numbers, Strings) and search by name/description
- Per-collection mode columns (Light, Dark, Custom) matching Figma's Variables panel
- Color swatches, alias resolution, and click-to-copy on any value
- Works without Enterprise plan via Sparrow Bridge (local mode)

### Design System Dashboard

A Lighthouse-style health scorecard that audits your design system across six categories.

**Usage:** Ask Claude to "audit the design system" or "show me design system health" while connected to a Figma file.

**Features:**
- Overall weighted score (0–100) with six category gauges: Naming, Tokens, Components, Accessibility, Consistency, Coverage
- Expandable category sections with individual findings, severity indicators, and actionable details
- Diagnostic locations linking findings to specific variables, components, or collections
- Tooltips explaining each check's purpose and scoring criteria
- Refresh button to re-run the audit without consuming AI context
- Pure scoring engine with no external dependencies — all analysis runs locally

**Enabling MCP Apps:**

MCP Apps are enabled by default in the setup configurations above (via `"ENABLE_MCP_APPS": "true"`). If you set up before v1.10.0 and don't have this in your config, add it to your `env` section:

```json
"env": {
  "FIGMA_ACCESS_TOKEN": "figd_YOUR_TOKEN_HERE",
  "ENABLE_MCP_APPS": "true"
}
```

> **Note:** MCP Apps require an MCP client with [ext-apps protocol](https://github.com/anthropics/anthropic-cookbook/tree/main/misc/model_context_protocol/ext-apps) support (e.g. Claude Desktop). This feature is experimental and the protocol may evolve.

### Future MCP Apps Roadmap

Planned MCP Apps:

- **Component Gallery** — Visual browser for searching and previewing components with variant exploration
- **Style Inspector** — Interactive panel for exploring color, text, and effect styles with live previews
- **Variable Diff Viewer** — Side-by-side comparison of token values across modes and branches

The architecture supports adding new apps with minimal boilerplate — each app is a self-contained module with its own server-side tool registration and client-side UI.

---

## 🚀 Advanced Topics

- **[Setup Guide](docs/SETUP.md)** - Complete setup guide
- **[Architecture](docs/ARCHITECTURE.md)** - How it works under the hood
- **[Troubleshooting](docs/TROUBLESHOOTING.md)** - Common issues and solutions

---

## 🤝 vs. Figma Official MCP

**Figma Sparrow MCP (This Project)** - Debugging & data extraction
- ✅ Real-time console logs from Figma plugins
- ✅ Screenshot capture and visual debugging
- ✅ Error stack traces and runtime monitoring
- ✅ Raw design data extraction (JSON)
- ✅ Works locally

**Figma Official Dev Mode MCP** - Code generation
- ✅ Generates React/HTML code from designs
- ✅ Tailwind/CSS class generation
- ✅ Component boilerplate scaffolding

**Use both together** for the complete workflow: generate code with Official MCP, then debug and extract data with Sparrow MCP.

---

## 🛤️ Roadmap

**Current Status:** v1.11.1 (Stable) - Production-ready with WebSocket-only connectivity, smart multi-file tracking, 56+ tools, Comments API, and MCP Apps

**Recent Releases:**
- [x] **v1.11.1** - Doc generator fixes: clean markdown tables, Storybook links, property metadata filtering
- [x] **v1.11.0** - Complete CDP removal, improved multi-file active tracking with focus detection
- [x] **v1.10.0** - Multi-instance support (dynamic port fallback 9223–9232, multi-connection plugin, instance discovery)
- [x] **v1.9.0** - Figma Comments tools, improved port conflict detection
- [x] **v1.8.0** - WebSocket Bridge transport (CDP-free connectivity), real-time selection/document tracking, `figma_get_selection` + `figma_get_design_changes` tools
- [x] **v1.7.0** - MCP Apps (Token Browser, Design System Dashboard), batch variable operations, design-code parity tools
- [x] **v1.5.0** - Node manipulation tools, component property management, component set arrangement
- [x] **v1.3.0** - Design creation via `figma_execute`, variable CRUD operations

**Coming Next:**
- [ ] **Component template library** - Common UI pattern generation
- [ ] **Visual regression testing** - Screenshot diff capabilities
- [ ] **Design linting** - Automated compliance and accessibility checks
- [ ] **AI enhancements** - Intelligent component suggestions and auto-layout optimization

**📖 [Full Roadmap](docs/ROADMAP.md)**

---

## 💻 Development

```bash
git clone https://github.com/TrueSparrowSystems/figma-sparrow-mcp.git
cd figma-sparrow-mcp
npm install

# Local mode development
npm run dev:local

# Cloud mode development
npm run dev

# Build
npm run build
```

**📖 [Development Guide](docs/ARCHITECTURE.md)**

---

## 🔗 Links

- 📖 [Local Docs](docs/) — Documentation source files
- 🐛 [Report Issues](https://github.com/TrueSparrowSystmes/figma-sparrow-mcp/issues)
- 💬 [Discussions](https://github.com/TrueSparrowSystems/figma-sparrow-mcp/discussions)
- 🌐 [Model Context Protocol](https://modelcontextprotocol.io/)
- 🎨 [Figma API](https://www.figma.com/developers/api)
