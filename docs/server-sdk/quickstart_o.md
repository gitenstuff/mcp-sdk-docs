# Quickstart

Get a Model Context Protocol (MCP) server running in minutes.

## Prerequisites
*   Node.js 18 or higher
*   npm, yarn, or pnpm

## 1. Installation

```bash
npm install @modelcontextprotocol/sdk zod
```

## 2. Create the Server

Create a file named `server.ts`. This example creates a server that runs on `stdio` (standard input/output), which is perfect for testing with local LLM clients or CLI tools.

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

// 1. Initialize the server
const server = new McpServer({
  name: "my-first-server",
  version: "1.0.0"
});

// 2. Register a Tool
server.registerTool(
  {
    name: "calculate-sum",
    description: "Add two numbers together",
    args: {
      a: z.number(),
      b: z.number()
    }
  },
  async ({ a, b }) => {
    return {
      content: [{ type: "text", text: String(a + b) }]
    };
  }
);

// 3. Register a Resource
server.registerResource(
  {
    name: "app-config",
    uri: "file:///config.json",
    mimeType: "application/json"
  },
  async (uri) => {
    return {
      contents: [{
        uri: uri.href,
        mimeType: "application/json",
        text: JSON.stringify({ appName: "My App", debug: true })
    };
  }
);

// 4. Register a Prompt
server.registerPrompt(
  {
    name: "greet",
    description: "Greets the user",
    args: { name: z.string() }
  },
  async ({ name }) => {
    return {
      messages: [{ role: "user", content: { type: "text", text: `Hello, ${name}!` } }]
    };
  }
);

// 5. Connect via Transport
// The Stdio transport allows this server to be run as a subprocess
const transport = new StdioServerTransport();
await server.connect(transport);

console.error("MCP Server running on stdio");
```

## 3. Run It

You can run this directly with `tsx` (TypeScript Execute) or compile it first.

```bash
npx tsx server.ts
```

*Note: Since it listens on Stdio, it will appear to "hang". This is normal. It is waiting for JSON-RPC messages on stdin.*

## 4. Testing (HTTP Server)

If you prefer an HTTP server (e.g., to connect from a remote client), switch the transport:

```typescript
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import http from "node:http";

// ... (Server setup remains the same) ...

const httpTransport = new StreamableHTTPServerTransport({
    sessionIdGenerator: () => crypto.randomUUID(),
});

await server.connect(httpTransport);

const httpServer = http.createServer(async (req, res) => {
    await httpTransport.handleRequest(req, res);
});

httpServer.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
});
```

For HTTP, you can test the connection using `curl`:

```bash
curl http://localhost:3000/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "initialize",
    "params": {
      "protocolVersion": "2024-11-05",
      "capabilities": {},
      "clientInfo": { "name": "test-client", "version": "1.0" }
    }
  }'
```