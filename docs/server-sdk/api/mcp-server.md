# McpServer Class API

The `McpServer` class is the high-level entry point for building an MCP server. It manages capabilities (Tools, Resources, Prompts), handles connections, and routes requests.

## Index

*   [Constructor](#constructor)
*   [Connection Management](#connection-management)
    *   [connect](#connecttransport)
    *   [close](#close)
*   [Registering Capabilities](#registering-capabilities)
    *   [tool](#toolname-schema-callback)
    *   [resource](#resourcename-uriortemplate-callback)
    *   [prompt](#promptname-schema-callback)
*   [Utility Methods](#utility-methods)

## Constructor

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";

const server = new McpServer(
  {
    name: "my-server-name",
    version: "1.0.0"
  },
  {
    capabilities: {
      logging: {},
      resources: {
        subscribe: true,
        listChanged: true
      },
      tools: {
        listChanged: true
      },
      prompts: {
        listChanged: true
      }
    }
  }
);
```

### Parameters
1.  **`serverInfo`** (`Implementation`): Metadata about your server.
    *   `name` (string): The name of your server.
    *   `version` (string): The version of your server.
2.  **`options`** (`ServerOptions`, optional):
    *   `capabilities` (`ServerCapabilities`): Explicitly declare capabilities.
        *   `logging` (`object`): Enable logging support.
        *   `resources` (`object`):
            *   `subscribe` (`boolean`): Allow clients to subscribe to resource updates.
            *   `listChanged` (`boolean`): Notify clients when the resource list changes.
        *   `tools` (`object`):
            *   `listChanged` (`boolean`): Notify clients when the tool list changes.
        *   `prompts` (`object`):
            *   `listChanged` (`boolean`): Notify clients when the prompt list changes.
        *   `experimental` (`object`): For non-standard capabilities.
    *   `enforceStrictCapabilities` (boolean): Whether to strict check capability negotiation.

---

## Connection Management

### `connect(transport)`

Attaches the server to a transport (Stdio, SSE, HTTP) and starts listening for messages.

```typescript
await server.connect(transport);
```

*   **transport**: An instance of a `Transport` (e.g., `StdioServerTransport`).
*   **Returns**: `Promise<void>`

### `close()`

Closes the connection and stops the server.

```typescript
await server.close();
```

---

## Registering Capabilities

### `tool(name, schema, callback)`

Registers a tool that clients can call. Uses Zod schemas for runtime argument validation.

```typescript
import { z } from "zod";

server.tool(
  "calculate-sum",
  "Calculates the sum of two numbers",
  { 
    a: z.number().describe("First number"),
    b: z.number().describe("Second number") 
  },
  async ({ a, b }) => {
    return {
      content: [
        {
          type: "text",
          text: String(a + b)
        }
      ]
    };
  }
);
```

*   **`name`** (string): Unique name of the tool.
*   **`description`** (string, optional): Human-readable description.
*   **`schema`** (Zod Object Shape): An object where keys are argument names and values are Zod schemas.
*   **`callback`** (function): The function to execute.
    *   Receives: `args` (inferred from schema), `extra` (request context).
    *   Returns: `CallToolResult` (content array) or `Promise<CallToolResult>`.

### `resource(name, uriOrTemplate, callback)`

Exposes a resource to clients. Can be a static URI or a template.

**Static Resource:**
```typescript
server.resource(
  "app-logs",
  "file:///var/log/app.log",
  async (uri) => {
    const logContent = await readLogFile();
    return {
      contents: [{
        uri: uri.href,
        mimeType: "text/plain",
        text: logContent
      }]
    };
  }
);
```

**Template Resource:**
```typescript
server.resource(
  "user-profile",
  "file:///users/{id}/profile",
  async (uri, { id }) => {
    const user = await db.getUser(id);
    return {
      contents: [{
        uri: uri.href,
        mimeType: "application/json",
        text: JSON.stringify(user)
      }]
    };
  }
);
```

*   **`name`** (string): Internal name for the resource registry.
*   **`uriOrTemplate`** (string): A fixed URI (e.g., `file:///a.txt`) or a template (e.g., `file:///{id}.txt`).
*   **`callback`** (function): 
    *   Receives: `uri` (URL object), `variables` (object extracted from template), `extra` (context).
    *   Returns: `ReadResourceResult` (contents array).

### `prompt(name, schema, callback)`

Registers a prompt template that clients can use.

```typescript
server.prompt(
  "code-review",
  "Generates a code review prompt",
  { language: z.string() },
  async ({ language }) => {
    return {
      messages: [
        {
          role: "user",
          content: {
            type: "text",
            text: `Please review this ${language} code:`
          }
        }
      ]
    };
  }
);
```

*   **`name`** (string): Unique name of the prompt.
*   **`description`** (string, optional): Human-readable description.
*   **`schema`** (Zod Object Shape): Arguments required by the prompt.
*   **`callback`** (function):
    *   Receives: `args` (inferred from schema).
    *   Returns: `GetPromptResult` (messages array).

---

## Utility Methods

### `isConnected()`
Returns `true` if the server has an active transport connection.

### `server` (Property)
Access the low-level `Server` instance for advanced operations like sending custom notifications or manually handling requests that `McpServer` doesn't wrap.

```typescript
server.server.sendLoggingMessage({
  level: "info",
  data: "Server started"
});
```