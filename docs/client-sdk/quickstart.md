# Quickstart

Connect to an MCP server and call a tool.

## Prerequisites
*   Node.js 18 or higher
*   npm, yarn, or pnpm

## 1. Installation

```bash
npm install @modelcontextprotocol/sdk zod
```

## 2. Connect to a Server

This example connects to a local server running on Stdio.

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";

// 1. Create the transport
// This starts the server as a subprocess
const transport = new StdioClientTransport({
  command: "npx",
  args: ["-y", "@modelcontextprotocol/server-everything"]
});

// 2. Create the client
const client = new Client(
  {
    name: "my-client",
    version: "1.0.0"
  },
  {
    capabilities: {
      prompts: {},
      resources: {},
      tools: {}
    }
  }
);

// 3. Connect
await client.connect(transport);

// 4. List Tools
const tools = await client.listTools();
console.log("Available tools:", tools);

// 5. Call a Tool
const result = await client.callTool({
  name: "echo",
  arguments: { message: "Hello MCP!" }
});

console.log("Result:", result);
```
