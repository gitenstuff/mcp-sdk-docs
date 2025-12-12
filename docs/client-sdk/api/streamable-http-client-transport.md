# StreamableHTTPClientTransport Class API

Connects to an MCP server via HTTP (Server-Sent Events). This is the recommended transport for HTTP connections.

## Index

*   [Constructor](#constructor)

## Constructor

```typescript
import { StreamableHTTPClientTransport } from "@modelcontextprotocol/sdk/client/streamableHttp.js";

const transport = new StreamableHTTPClientTransport(
  new URL("http://localhost:3000/mcp")
);
```

### Parameters
1.  **`url`** (`URL`): The URL of the MCP server.
2.  **`options`** (`object`, optional):
    *   `authProvider` (`OAuthClientProvider`, optional): Provider for handling OAuth authentication.
    *   `requestInit` (`RequestInit`, optional): Custom options for the HTTP request (e.g., headers).
    *   `fetch` (`FetchLike`, optional): Custom fetch implementation.
    *   `reconnectionOptions` (`StreamableHTTPReconnectionOptions`, optional): Options for handling reconnection.
    *   `sessionId` (`string`, optional): A specific session ID to use.
