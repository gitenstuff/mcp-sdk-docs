# SSEClientTransport Class API

Connects to an MCP server via HTTP (Server-Sent Events). **This transport is deprecated and should not be used for new development.** Use `StreamableHTTPClientTransport` instead.

## Index

*   [Constructor](#constructor)

## Constructor

```typescript
import { SSEClientTransport } from "@modelcontextprotocol/sdk/client/sse.js";

const transport = new SSEClientTransport(
  new URL("http://localhost:3000/sse")
);
```

### Parameters
1.  **`url`** (`URL`): The URL of the MCP server.
2.  **`options`** (`object`, optional):
    *   `eventSourceInit` (`EventSourceInit`, optional): Initialization options for the EventSource connection.
    *   `requestInit` (`RequestInit`, optional): Custom options for the HTTP request.
