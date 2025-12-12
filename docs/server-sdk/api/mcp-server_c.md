# McpServer Class API

The `McpServer` class is the high-level entry point for building an MCP server. It manages capabilities (Tools, Resources, Prompts), handles connections, and routes requests.

## Index

*   [Constructor](#constructor)
*   [Connection Management](#connection-management)
    *   [connect](#connect)
    *   [close](#close)
*   [Registering Capabilities](#registering-capabilities)
    *   [registerTool](#registertool)
    *   [registerResource](#registerresource)
    *   [registerPrompt](#registerprompt)
    *   [tool (Deprecated)](#tool-deprecated)
    *   [resource (Deprecated)](#resource-deprecated)
    *   [prompt (Deprecated)](#prompt-deprecated)
*   [Utility Methods](#utility-methods)
    *   [isConnected](#isconnected)
    *   [server](#server)
    *   [onClientConnect](#onclientconnect)
    *   [onClientDisconnect](#onclientdisconnect)

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
    *   `name` (`string`): The name of your server.
    *   `version` (`string`): The version of your server.
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
    *   `enforceStrictCapabilities` (`boolean`): Whether to strict check capability negotiation.

---

## Connection Management

### `connect(transport)`

Attaches the server to a transport (Stdio, SSE, HTTP) and starts listening for messages.

```typescript
await server.connect(transport);
```

*   **`transport`** (`Transport`): An instance of a `Transport` (e.g., `StdioServerTransport`).
*   **Returns**: `Promise<void>` - Resolves when the connection is established.

### `close()`

Closes the connection and stops the server.

```typescript
await server.close();
```

*   **Returns**: `Promise<void>` - Resolves when the server is closed.

---

## Registering Capabilities

### `registerTool(options, callback)`

Registers a tool that clients can call. Uses Zod schemas for runtime argument validation.

```typescript
import { z } from "zod";

server.registerTool(
  {
    name: "calculate-sum",
    description: "Calculates the sum of two numbers",
    args: {
      a: z.number().describe("First number"),
      b: z.number().describe("Second number")
    }
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

*   **`options`** (`object`): Tool configuration.
    *   `name` (`string`): Unique name of the tool.
    *   `description` (`string`, optional): Human-readable description.
    *   `args` (`Zod Object Shape`): An object where keys are argument names and values are Zod schemas.
*   **`callback`** (`function`): The function to execute.
    *   Receives: `args` (inferred from schema), `extra` (request context).
    *   Returns: `CallToolResult` (content array) or `Promise<CallToolResult>`.

### `registerResource(options, callback)`

Exposes a resource to clients. Can be a static URI or a template.

**Static Resource:**
```typescript
server.registerResource(
  {
    name: "app-logs",
    uri: "file:///var/log/app.log",
    mimeType: "text/plain" 
  },
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
server.registerResource(
  {
    name: "user-profile",
    template: "file:///users/{id}/profile",
    mimeType: "application/json"
  },
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

*   **`options`** (`object`): Resource configuration.
    *   `name` (`string`): Internal name for the resource registry.
    *   `uri` (`string`, optional): A fixed URI (e.g., `file:///a.txt`).
    *   `template` (`string`, optional): A URI template (e.g., `file:///{id}.txt`).
    *   `mimeType` (`string`, optional): The MIME type of the resource.
*   **`callback`** (`function`): 
    *   Receives: `uri` (URL object), `variables` (object extracted from template), `extra` (context).
    *   Returns: `ReadResourceResult` (contents array).

### `registerPrompt(options, callback)`

Registers a prompt template that clients can use.

```typescript
server.registerPrompt(
  {
    name: "code-review",
    description: "Generates a code review prompt",
    args: { language: z.string() }
  },
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

*   **`options`** (`object`): Prompt configuration.
    *   `name` (`string`): Unique name of the prompt.
    *   `description` (`string`, optional): Human-readable description.
    *   `args` (`Zod Object Shape`): Arguments required by the prompt.
*   **`callback`** (`function`):
    *   Receives: `args` (inferred from schema).
    *   Returns: `GetPromptResult` (messages array).

### `tool(name, schema, callback)` (Deprecated)

**Deprecated:** Use [`registerTool`](#registertool) instead.

Registers a tool that clients can call.

*   **`name`** (`string`): Unique name of the tool.
*   **`description`** (`string`, optional): Human-readable description.
*   **`schema`** (`Zod Object Shape`): An object where keys are argument names and values are Zod schemas.
*   **`callback`** (`function`): The function to execute.
    *   Receives: `args` (inferred from schema), `extra` (request context).
    *   Returns: `CallToolResult` (content array) or `Promise<CallToolResult>`.

### `resource(name, uriOrTemplate, callback)` (Deprecated)

**Deprecated:** Use [`registerResource`](#registerresource) instead.

Exposes a resource to clients.

*   **`name`** (`string`): Internal name for the resource registry.
*   **`uriOrTemplate`** (`string`): A fixed URI or a template.
*   **`callback`** (`function`): 
    *   Receives: `uri` (URL object), `variables` (object extracted from template), `extra` (context).
    *   Returns: `ReadResourceResult` (contents array).

### `prompt(name, schema, callback)` (Deprecated)

**Deprecated:** Use [`registerPrompt`](#registerprompt) instead.

Registers a prompt template that clients can use.

*   **`name`** (`string`): Unique name of the prompt.
*   **`description`** (`string`, optional): Human-readable description.
*   **`schema`** (`Zod Object Shape`): Arguments required by the prompt.
*   **`callback`** (`function`):
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

### `onClientConnect(callback)`

Registers a callback to be invoked when a client connects.

```typescript
server.onClientConnect((clientId) => {
  console.log(`Client connected: ${clientId}`);
});
```

*   **`callback`** (`(clientId: string) => void`): Function to call on client connection.

### `onClientDisconnect(callback)`

Registers a callback to be invoked when a client disconnects.

```typescript
server.onClientDisconnect((clientId) => {
  console.log(`Client disconnected: ${clientId}`);
});
```

*   **`callback`** (`(clientId: string) => void`): Function to call on client disconnection.
