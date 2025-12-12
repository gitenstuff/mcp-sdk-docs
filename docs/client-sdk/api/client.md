# Client Class API

The `Client` class is the primary interface for interacting with MCP servers.

## Index

*   [Constructor](#constructor)
*   [Connection Management](#connection-management)
    *   [connect](#connecttransport)
    *   [close](#close)
*   [Request Methods](#request-methods)
    *   [listTools](#listtools)
    *   [callTool](#calltoolparams)
    *   [listResources](#listresources)
    *   [readResource](#readresourceparams)
    *   [listPrompts](#listprompts)
    *   [getPrompt](#getpromptparams)

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
1.  **`clientInfo`** (`ClientImplementation`): Metadata about your client.
    *   `name` (`string`): The name of your client.
    *   `version` (`string`): The version of your client.
2.  **`options`** (`ClientOptions`, optional):
    *   `capabilities` (`ClientCapabilities`): Declare what your client supports.
        *   `sampling` (`object`, optional): Enable sampling support.
        *   `roots` (`object`, optional): Enable filesystem roots support.

---

## Connection Management

### `connect(transport)`

Connects to the server and performs the initialization handshake (protocol version negotiation and capability exchange).

```typescript
await client.connect(transport);
```

*   **`transport`** (`Transport`): The transport instance to connect with (e.g., `StdioClientTransport`, `StreamableHTTPClientTransport`).
*   **Returns**: `Promise<void>` - Resolves when the connection is successfully established.

### `close()`

Closes the connection to the server.

```typescript
await client.close();
```

*   **Returns**: `Promise<void>` - Resolves when the connection is closed.

---

## Request Methods

### `listTools()`

Retrieves the list of tools available on the server.

```typescript
const result = await client.listTools();
console.log(result.tools); 
```

*   **Returns**: `Promise<{ tools: Tool[] }>`
    *   `tools` (`Tool[]`): An array of available tools.
        *   `name` (`string`): The tool name.
        *   `description` (`string`, optional): Tool description.
        *   `inputSchema` (`object`): JSON Schema for the tool's arguments.

### `callTool(params)`

Calls a tool on the server with specified arguments.

```typescript
const result = await client.callTool({
  name: "calculate-sum",
  arguments: { a: 1, b: 2 }
});
```

*   **`params`** (`object`):
    *   `name` (`string`): The name of the tool to call.
    *   `arguments` (`Record<string, unknown>`): The arguments for the tool, matching its input schema.
*   **Returns**: `Promise<CallToolResult>`
    *   `content` (`(TextContent | ImageContent | EmbeddedResource)[]`): The tool's output.
    *   `isError` (`boolean`, optional): `true` if the tool execution failed.

### `listResources()`

Lists available resources on the server.

```typescript
const result = await client.listResources();
```

*   **Returns**: `Promise<{ resources: Resource[] }>`
    *   `resources` (`Resource[]`): An array of available resources.
        *   `uri` (`string`): The unique URI of the resource.
        *   `name` (`string`): Human-readable name.
        *   `description` (`string`, optional): Description of the resource.
        *   `mimeType` (`string`, optional): The MIME type of the resource content.

### `readResource(params)`

Reads the content of a specific resource by its URI.

```typescript
const result = await client.readResource({
  uri: "file:///logs/app.log"
});
```

*   **`params`** (`object`):
    *   `uri` (`string`): The URI of the resource to read.
*   **Returns**: `Promise<ReadResourceResult>`
    *   `contents` (`(TextResourceContents | BlobResourceContents)[]`): The resource content.
        *   `uri` (`string`): The URI of the resource.
        *   `mimeType` (`string`, optional): MIME type.
        *   `text` (`string`, optional): Text content (for text resources).
        *   `blob` (`string`, optional): Base64 encoded binary data (for blob resources).

### `listPrompts()`

Lists available prompts on the server.

```typescript
const result = await client.listPrompts();
```

*   **Returns**: `Promise<{ prompts: Prompt[] }>`
    *   `prompts` (`Prompt[]`): An array of available prompts.
        *   `name` (`string`): The prompt name.
        *   `description` (`string`, optional): Description.
        *   `arguments` (`object[]`, optional): List of arguments the prompt accepts.

### `getPrompt(params)`

Retrieves a prompt template instantiated with specific arguments.

```typescript
const result = await client.getPrompt({
  name: "git-commit",
  arguments: { changes: "diff..." }
});
```

*   **`params`** (`object`):
    *   `name` (`string`): The name of the prompt to retrieve.
    *   `arguments` (`Record<string, string>`, optional): Arguments to fill the prompt template.
*   **Returns**: `Promise<GetPromptResult>`
    *   `description` (`string`, optional): Description of the instantiated prompt.
    *   `messages` (`PromptMessage[]`): The messages to send to the LLM.
        *   `role` (`"user" | "assistant"`): The message role.
        *   `content` (`object`): The message content (text, image, or resource).
