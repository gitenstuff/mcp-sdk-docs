# Transports API

## `StdioClientTransport`

Starts a server as a subprocess and communicates via standard input/output.

### Constructor

```typescript
new StdioClientTransport({
  command: string,  // Executable (e.g., "node", "npx", "python")
  args?: string[],  // Arguments
  env?: object,     // Environment variables
  stderr?: "inherit" | "ignore" // Handle server stderr
})
```

### Example

```typescript
const transport = new StdioClientTransport({
  command: "python3",
  args: ["server.py"]
});
```

---

## `StreamableHTTPClientTransport`

Connects to an MCP server via HTTP (Server-Sent Events). This is the recommended transport for HTTP connections.

### Constructor

```typescript
new StreamableHTTPClientTransport(
  url: URL,
  options?: {
    authProvider?: OAuthClientProvider;
    requestInit?: RequestInit;
    fetch?: FetchLike;
    reconnectionOptions?: StreamableHTTPReconnectionOptions;
    sessionId?: string;
  }
)
```

### Example

```typescript
const transport = new StreamableHTTPClientTransport(
  new URL("http://localhost:3000/mcp")
);
```

---

## `SSEClientTransport` (Deprecated)

Connects to an MCP server via HTTP (Server-Sent Events). **This transport is deprecated and should not be used for new development.** Use `StreamableHTTPClientTransport` instead.

### Constructor

```typescript
new SSEClientTransport(
  url: URL,
  options?: {
    eventSourceInit?: EventSourceInit,
    requestInit?: RequestInit
  }
)
```

### Example

```typescript
const transport = new SSEClientTransport(
  new URL("http://localhost:3000/sse")
);
```