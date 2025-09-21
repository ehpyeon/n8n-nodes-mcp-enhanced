# n8n-nodes-mcp-enhanced

> **Enhanced MCP Client for n8n with AI Agent Support**

This is an enhanced version of the n8n community node that lets you interact with Model Context Protocol (MCP) servers in your n8n workflows, with special optimizations for AI Agent integration.

**Key Enhancement**: Tool Name and Tool Parameters are now fully JSON-compatible, allowing AI Agents to automatically select and configure MCP tools without manual intervention.

MCP is a protocol that enables AI models to interact with external tools and data sources in a standardized way. This node allows you to connect to MCP servers, access resources, execute tools, and use prompts.

[n8n](https://n8n.io/) is a [fair-code licensed](https://docs.n8n.io/reference/license/) workflow automation platform.

## What's Enhanced

### AI Agent Compatibility
- **Automatic Tool Selection**: AI Agents can now automatically select tools by name using JSON parameters
- **Structured Parameters**: Both tool names and parameters are JSON-compatible for seamless AI integration
- **Backward Compatibility**: All existing workflows continue to work unchanged

### Enhanced Parameter Handling
The node now supports multiple parameter formats:
```json
// AI Agent format (recommended)
[{
  "Tool_Parameters": {
    "tool_name": "search_arxiv", 
    "parameters": {
      "query": "artificial intelligence",
      "max_results": 10
    }
  }
}]

// Direct format
{
  "Tool_Parameters": {
    "tool_name": "search_arxiv",
    "parameters": {...}
  }
}

// Legacy format (still supported)
Tool Name: "search_arxiv"
Tool Parameters: {"query": "...", "max_results": 10}
```

## Installation

Follow the [installation guide](https://docs.n8n.io/integrations/community-nodes/installation/) in the n8n community nodes documentation.

### Install via npm
```bash
npm install n8n-nodes-mcp-enhanced
```

### Install in n8n
1. Go to **Settings** > **Community Nodes**
2. Enter package name: `n8n-nodes-mcp-enhanced`
3. Click **Install**

**Important**: Set the `N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE` environment variable to `true` if you want to use the MCP Client node as a tool in AI Agents.

## Credentials

The MCP Client node supports three types of credentials to connect to an MCP server:

### Command-line Based Transport (STDIO)
- **Command**: The command to start the MCP server
- **Arguments**: Optional arguments to pass to the server command  
- **Environment Variables**: Variables to pass to the server in NAME=VALUE format

### HTTP Streamable Transport (Recommended)
- **HTTP Streamable URL**: The HTTP endpoint that supports streaming responses
- **Additional Headers**: Optional headers to send with requests

### Server-Sent Events (SSE) Transport (Legacy)
- **SSE URL**: The URL of the SSE endpoint
- **Messages Post Endpoint**: Optional custom endpoint for posting messages
- **Additional Headers**: Optional headers to send with requests

## Operations

- **Execute Tool** - Execute a specific tool with parameters (Enhanced for AI Agents)
- **Get Prompt** - Get a specific prompt template
- **List Prompts** - Get a list of available prompts
- **List Resources** - Get a list of available resources from the MCP server
- **List Tools** - Get a list of available tools
- **Read Resource** - Read a specific resource by URI

## Using with AI Agents

### Enable Tool Usage
Set environment variable:
```bash
export N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true
```

### AI Agent Integration
1. Add MCP Client node to your workflow
2. Configure credentials for your MCP server
3. In AI Agent node, enable MCP Client as a tool
4. AI Agent will automatically handle tool selection and parameter formatting

### Example AI Agent Prompt
```
Search for recent papers about "transformer architecture" using arXiv, 
then summarize the top 3 results.
```

The AI Agent will automatically:
1. Select the appropriate MCP tool
2. Format parameters correctly
3. Execute the search
4. Process results

## Environment Variables

Support for Docker environment variables with `MCP_` prefix:

```yaml
version: '3'
services:
  n8n:
    image: n8nio/n8n
    environment:
      - MCP_BRAVE_API_KEY=your-api-key
      - MCP_OPENAI_API_KEY=your-openai-key
      - N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true
```

## Compatibility

- Requires n8n version 1.0.0 or later
- Compatible with MCP Protocol version 1.0.0 or later
- Supports STDIO, HTTP Streamable, and SSE transports
- Fully backward compatible with existing workflows

## Original Project

This is an enhanced version based on [n8n-nodes-mcp](https://github.com/nerding-io/n8n-nodes-mcp) by Jd Fiscus.

## License

MIT License - See [LICENSE.md](LICENSE.md) for details.

## Resources

* [n8n community nodes documentation](https://docs.n8n.io/integrations/community-nodes/)
* [Model Context Protocol Documentation](https://modelcontextprotocol.io/docs/)
* [MCP TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk)
* [Original n8n-nodes-mcp project](https://github.com/nerding-io/n8n-nodes-mcp)