# Client Class API

The `Client` class is the primary interface for interacting with MCP servers.

## Constructor

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";

const client = new Client(
  {
    name: "my-client",
    version: "1.0.0"
  },
  {
    capabilities: {
      sampling: {} // If you want to support sampling
    }
  }
);
```

### Parameters
1.  **`clientInfo`** (`Implementation`): Metadata about your client (`name`, `version`).
2.  **`options`** (`ClientOptions`, optional):
    *   **`capabilities`**: Declare what your client supports (e.g., `sampling`, `roots`).

---

## Connection Management

### `connect(transport)`

Connects to the server and performs the initialization handshake.

```typescript
await client.connect(transport);
```

### `close()`

Closes the connection.

```typescript
await client.close();
```

---

## Request Methods

### `listTools()`
Retrieves the list of tools available on the server.

### `callTool(params)`
Calls a tool.
*   **`params`**: `{ name: string, arguments: object }`
*   **Returns**: `CallToolResult`

```typescript
const result = await client.callTool({
  name: "add",
  arguments: { a: 1, b: 2 }
});
```

### `listResources()`
Lists available resources.

### `readResource(params)`
Reads a resource by URI.
*   **`params`**: `{ uri: string }`

```typescript
const result = await client.readResource({
  uri: "file:///logs/app.log"
});
```

### `listPrompts()`
Lists available prompts.

### `getPrompt(params)`
Gets a prompt with arguments.
*   **`params`**: `{ name: string, arguments?: object }`

```typescript
const result = await client.getPrompt({
  name: "git-commit",
  arguments: { changes: "diff..." }
});
```
