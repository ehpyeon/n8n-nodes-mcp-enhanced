# n8n-nodes-mcp-enhanced

[![npm version](https://img.shields.io/npm/v/n8n-nodes-mcp-enhanced?color=EA4B71&label=npm&logo=npm)](https://www.npmjs.com/package/n8n-nodes-mcp-enhanced)
[![npm downloads](https://img.shields.io/npm/dw/n8n-nodes-mcp-enhanced?color=EA4B71&logo=npm)](https://www.npmjs.com/package/n8n-nodes-mcp-enhanced)
[![n8n community node](https://img.shields.io/badge/n8n-community%20node-EA4B71?logo=n8n&logoColor=white)](https://docs.n8n.io/integrations/community-nodes/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE.md)

> **An enhanced [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) client node for [n8n](https://n8n.io/) — built so AI Agents can drive MCP tools with zero manual wiring.**

Connect your n8n workflows to any MCP server to list, read, and execute tools, prompts, and resources. This package extends the community MCP node with **AI-Agent-native field control**: the agent sets the tool name and parameters directly, so it can pick and configure MCP tools on its own.

<p align="center">
  <img src="https://raw.githubusercontent.com/ehpyeon/n8n-nodes-mcp-enhanced/main/assets/mcp-n8n.png" alt="MCP Client node in an n8n workflow" width="720" />
</p>

---

## Table of Contents

- [Why this package](#why-this-package)
- [Quick Start](#quick-start)
- [Operations](#operations)
- [Credentials & Transports](#credentials--transports)
- [Using with AI Agents](#using-with-ai-agents)
- [Parameter Formats](#parameter-formats)
- [Environment Variables](#environment-variables)
- [Compatibility](#compatibility)
- [Credits](#credits)
- [License](#license)

---

## Why this package

This is an enhanced build of the community MCP node, focused on one thing: **making MCP tools first-class citizens for n8n AI Agents.**

| Capability | What it gives you |
|------------|-------------------|
| 🤖 **AI-Agent direct field control** | The agent sets `Tool Name` and `Tool Parameters` itself via n8n's *"let the model define this parameter"* — no manual mapping per tool |
| 🔀 **Flexible parameter parsing** | Accepts direct, nested, and legacy formats with intelligent fallback |
| 🌊 **HTTP Streamable transport** | Modern streaming transport in addition to STDIO and SSE |
| ♻️ **Backward compatible** | Existing workflows built on the standard MCP node keep working unchanged |

> Built on top of the excellent [`n8n-nodes-mcp`](https://github.com/nerding-io/n8n-nodes-mcp) by Jd Fiscus — see [Credits](#credits).

---

## Quick Start

### 1. Install in n8n

**Settings → Community Nodes → Install**, then enter:

```
n8n-nodes-mcp-enhanced
```

Or via npm:

```bash
npm install n8n-nodes-mcp-enhanced
```

### 2. Allow tool usage (for AI Agents)

Set this environment variable so the node can be used as a tool inside AI Agent nodes:

```bash
N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true
```

### 3. Add credentials and start calling MCP tools

<p align="center">
  <img src="https://raw.githubusercontent.com/ehpyeon/n8n-nodes-mcp-enhanced/main/assets/operations.png" alt="Available operations" width="360" />
</p>

---

## Operations

| Operation | Description |
|-----------|-------------|
| **Execute Tool** | Execute a specific tool with parameters (enhanced for AI Agents) |
| **List Tools** | Get the list of tools exposed by the MCP server |
| **Get Prompt** | Retrieve a specific prompt template |
| **List Prompts** | List available prompts |
| **Read Resource** | Read a specific resource by URI |
| **List Resources** | List available resources |

<p align="center">
  <img src="https://raw.githubusercontent.com/ehpyeon/n8n-nodes-mcp-enhanced/main/assets/list-tools.png" alt="List Tools result" width="380" />
  &nbsp;&nbsp;
  <img src="https://raw.githubusercontent.com/ehpyeon/n8n-nodes-mcp-enhanced/main/assets/execute-tool-result.png" alt="Execute Tool result" width="380" />
</p>

---

## Credentials & Transports

The MCP Client node supports three transport types.

<p align="center">
  <img src="https://raw.githubusercontent.com/ehpyeon/n8n-nodes-mcp-enhanced/main/assets/credentials.png" alt="Credential configuration" width="420" />
</p>

### HTTP Streamable (recommended)
- **HTTP Streamable URL** — endpoint that supports streaming responses
- **Additional Headers** — optional headers per request

### Command-line / STDIO
- **Command** — command to start the MCP server
- **Arguments** — optional arguments
- **Environment Variables** — `NAME=VALUE` pairs passed to the server

### Server-Sent Events (SSE, legacy)
- **SSE URL** — the SSE endpoint
- **Messages Post Endpoint** — optional custom post endpoint
- **Additional Headers** — optional headers

<p align="center">
  <img src="https://raw.githubusercontent.com/ehpyeon/n8n-nodes-mcp-enhanced/main/assets/credentials-envs.png" alt="Environment variable credentials" width="420" />
</p>

---

## Using with AI Agents

1. Add the **MCP Client** node to your workflow and configure credentials.
2. In the **AI Agent** node, enable the MCP Client as a tool.
3. The agent automatically controls both `Tool Name` and `Tool Parameters`.

**Example prompt:**

```
Search arXiv for recent papers on "transformer architecture",
then summarize the top 3 results.
```

The agent will, on its own:
1. Set `Tool Name` → `search_arxiv`
2. Set `Tool Parameters` → `{"query": "transformer architecture", "max_results": 3}`
3. Execute via the MCP protocol and return parsed results

<p align="center">
  <img src="https://raw.githubusercontent.com/ehpyeon/n8n-nodes-mcp-enhanced/main/assets/brave-search-example.png" alt="AI Agent using MCP tools" width="640" />
</p>

---

## Parameter Formats

The node parses parameters with the following priority: **Tool Name field → direct parameters → nested fallback → legacy**.

```jsonc
// 1. AI Agent direct control (v1.0.5+, recommended)
Tool Name: "search_arxiv"
Tool Parameters: { "query": "artificial intelligence", "max_results": 10 }

// 2. Nested format (legacy, still supported)
Tool Name: ""
Tool Parameters: [{
  "Tool_Parameters": {
    "tool_name": "search_arxiv",
    "parameters": { "query": "artificial intelligence", "max_results": 10 }
  }
}]
```

If `Tool Name` is set it always takes precedence; otherwise the node extracts the tool from the nested `Tool_Parameters` structure.

---

## Environment Variables

Docker environment variables prefixed with `MCP_` are passed through to MCP servers:

```yaml
services:
  n8n:
    image: n8nio/n8n
    environment:
      - MCP_BRAVE_API_KEY=your-api-key
      - MCP_OPENAI_API_KEY=your-openai-key
      - N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true
```

<p align="center">
  <img src="https://raw.githubusercontent.com/ehpyeon/n8n-nodes-mcp-enhanced/main/assets/multi-server-example.png" alt="Multiple MCP servers in one workflow" width="640" />
</p>

---

## Compatibility

- Requires **n8n 1.0.0+**
- MCP protocol **1.0.0+**
- Transports: STDIO, HTTP Streamable, SSE
- Fully backward compatible with existing MCP-node workflows

---

## Credits

This package is an enhanced version of [**n8n-nodes-mcp**](https://github.com/nerding-io/n8n-nodes-mcp) by **Jd Fiscus ([@nerding-io](https://github.com/nerding-io))**. Huge thanks to the original author and contributors — this build adds AI-Agent-native field control on top of their foundation.

- [n8n community nodes documentation](https://docs.n8n.io/integrations/community-nodes/)
- [Model Context Protocol documentation](https://modelcontextprotocol.io/docs/)
- [MCP TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk)

## Contributing

Issues and PRs are welcome. If this node is useful to you, a ⭐ on the [repo](https://github.com/ehpyeon/n8n-nodes-mcp-enhanced) helps others find it.

## License

[MIT](LICENSE.md) © Euihyeon Pyeon
