# Security

This document outlines the security architecture and practices for Figma Sparrow MCP.

## Overview

Figma Sparrow MCP is designed with security as a priority. The project is **fully open source** (MIT licensed), allowing complete code auditing by security teams.

## Architecture Security

### Deployment Modes

**Local Mode (Recommended for Security-Sensitive Environments)**
- Runs entirely on `localhost` via stdio transport
- Zero external network calls
- All communication stays on the local machine
- Figma API calls go directly from your machine to Figma's servers

### Data Handling

| Aspect | Details |
|--------|---------|
| **Data Storage** | None. No design data is persisted or cached to disk. |
| **Telemetry** | None. No analytics, tracking, or usage data collection. |
| **Logging** | Local only. Logs stay on your machine and are not transmitted. |
| **Credentials** | Stored in your local MCP client configuration, never transmitted to third parties. |

## Authentication

Figma Sparrow MCP uses **Figma's native authentication** mechanisms:

### Personal Access Tokens (PATs)
- Generated in Figma account settings
- Stored locally in your MCP client config
- Never transmitted except to Figma's API (`api.figma.com`)
- Scoped permissions based on token configuration

### OAuth (Remote Mode)
- Uses Figma's official OAuth 2.0 flow
- Tokens managed via Figma's authorization servers
- No custom credential handling

## Code Execution (`figma_execute`)

The `figma_execute` tool allows running JavaScript in Figma's plugin context. Security considerations:

### Sandboxed Environment
- Code runs in Figma's **plugin sandbox**, not your system
- No filesystem access
- No network access outside Figma's plugin APIs
- Cannot access other browser tabs or system resources

### Scope Limitations
- Can only modify the currently open Figma file
- Requires Sparrow Bridge plugin to be explicitly running
- Changes are made to your Figma file (covered by Figma's version history)

### Mitigations
- All code execution is logged
- Sparrow Bridge must be manually started by the user
- No autonomous execution without user-initiated prompts

## Data Access Scope

Figma Sparrow MCP can access:

| Data Type | Access Level | Notes |
|-----------|--------------|-------|
| Variables/Tokens | Read | Requires Enterprise for REST API, or Sparrow Bridge |
| Components | Read | Component metadata and properties |
| Styles | Read | Color, text, and effect styles |
| File Structure | Read | Pages, frames, layers |
| Console Logs | Read | From plugins you're debugging |
| Design Modifications | Write | Only via `figma_execute` with Sparrow Bridge |

**Cannot access:**
- Your filesystem
- Other applications
- Browser data
- Network resources outside Figma
- Other Figma files (only the currently open file)

## Network Security

### API Endpoints
All network communication is limited to:
- `api.figma.com` - Figma's official REST API (HTTPS)
- `localhost` - Local Desktop Bridge communication
- `*.workers.dev` - Remote mode only (HTTPS)

### No External Dependencies at Runtime
- No third-party analytics services
- No CDN dependencies
- No external API calls beyond Figma

## Security Checklist for Evaluators

- [x] Open source and auditable (MIT license)
- [x] No data persistence or storage
- [x] No telemetry or analytics
- [x] Uses platform-native authentication (Figma OAuth/PATs)
- [x] Code execution sandboxed in Figma plugin environment
- [x] Local-only deployment option available
- [x] All network traffic encrypted (HTTPS)
- [x] No third-party runtime dependencies
- [x] Responsible disclosure process documented

## Questions?

For security-related inquiries, open a [GitHub Discussion](https://github.com/TrueSparrowSystems/figma-sparrow-mcp/discussions) or use the [private security advisory](https://github.com/TrueSparrowSystems/figma-sparrow-mcp/security/advisories/new) feature for sensitive matters.
