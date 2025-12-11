# Error Handling Patterns

Robust error handling is essential for creating reliable MCP servers. The SDK provides the `McpError` class and a set of standard `ErrorCode` constants to help you report issues in a way that clients can understand and handle gracefully.

## `McpError` Class

The `McpError` class extends the standard JavaScript `Error` class, adding a numeric `code` and optional `data` field. It is the standard way to throw protocol-aware errors from your tools, resources, and prompt handlers.

### Constructor

```typescript
new McpError(code: ErrorCode | number, message: string, data?: unknown)
```

*   **`code`**: A numeric error code (see below).
*   **`message`**: A human-readable description of the error.
*   **`data`**: (Optional) Additional structured data to help the client debug or handle the error.

### Example Usage

```typescript
import { McpError, ErrorCode } from "@modelcontextprotocol/sdk/types.js";

server.tool("get-user", { id: z.string() }, async ({ id }) => {
  const user = await db.findUser(id);
  
  if (!user) {
    // Throwing this will result in a JSON-RPC error response to the client
    throw new McpError(
      ErrorCode.InvalidParams,
      `User with ID ${id} not found`
    );
  }
  
  return { content: [{ type: "text", text: JSON.stringify(user) }] };
});
```

---

## Standard Error Codes

The `ErrorCode` enum provides standard JSON-RPC 2.0 and MCP-specific error codes. Using these ensures your server plays nicely with the broader ecosystem.

| Code | Name | Description |
| :--- | :--- | :--- |
| `-32700` | `ParseError` | Invalid JSON was received by the server. |
| `-32600` | `InvalidRequest` | The JSON sent is not a valid Request object. |
| `-32601` | `MethodNotFound` | The method does not exist / is not available. |
| `-32602` | `InvalidParams` | Invalid method parameter(s). Use this for validation errors (e.g., Zod failures). |
| `-32603` | `InternalError` | Internal JSON-RPC error. |
| `-32000` | `ServerError` | Generic server error. |

### When to use what?

*   **`ErrorCode.InvalidParams`**: Use this when arguments provided to a tool, resource template, or prompt don't make sense (e.g., "User ID not found", "Date format invalid").
*   **`ErrorCode.InternalError`**: Use this for unexpected crashes or bug states (e.g., "Database connection failed").

---

## Best Practices

### 1. Graceful Tool Failure

For tools, you often have a choice: **Throw an error** vs. **Return an error result**.

*   **Throwing `McpError`**: The tool call fails entirely. The LLM sees a protocol error. Use this for fundamental brokenness (e.g., "Tool arguments are invalid JSON").
*   **Returning `isError: true`**: The tool executed, but the operation failed. The LLM receives the error text and can try again or correct itself. **This is usually preferred for logic errors.**

```typescript
// PREFERRED for logic errors
server.tool("read-file", { path: z.string() }, async ({ path }) => {
  try {
    const data = await fs.readFile(path, "utf-8");
    return { content: [{ type: "text", text: data }] };
  } catch (err) {
    return {
      isError: true, // Tells the LLM "I tried, but it failed"
      content: [{ type: "text", text: `Error reading file: ${err.message}` }]
    };
  }
});
```

### 2. Logging

Always log internal errors on the server side before returning a sanitized message to the client. This prevents leaking sensitive stack traces or internal implementation details.

```typescript
try {
  await sensitiveOperation();
} catch (err) {
  console.error("Critical Failure:", err); // Log full details internally
  throw new McpError(ErrorCode.InternalError, "An internal error occurred."); // Send generic message
}
```

### 3. Zod Validation

The `McpServer` class automatically handles Zod validation errors for you. If a client sends arguments that don't match your schema, the SDK will automatically throw an `McpError` with `ErrorCode.InvalidParams` and a descriptive message. You do not need to manually check types inside your handler.