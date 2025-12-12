# StreamableHTTPServerTransport Class API

The `StreamableHTTPServerTransport` is the recommended HTTP transport, designed for modern web-based communication. It supports Server-Sent Events (SSE) for streaming responses, direct JSON responses, and resumability for robust client connections.

## Index

*   [Constructor](#constructor)
*   [Request Handling](#request-handling)
    *   [handleRequest](#handlerequestreq-res-parsedbody)
    *   [closeSSEStream](#closessestreamrequestid)

## Constructor

```typescript
import { StreamableHTTPServerTransport } from '@modelcontextprotocol/sdk/server/streamableHttp.js';
import { randomUUID } from 'node:crypto';

const transport = new StreamableHTTPServerTransport({
  sessionIdGenerator: () => randomUUID(),
  enableJsonResponse: true
});
```

### Parameters
1.  **`options`** (`StreamableHTTPServerTransportOptions`): Configuration options.
    *   `sessionIdGenerator` (`() => string | undefined`): A function to generate unique session IDs. Return `undefined` for stateless operation.
    *   `onsessioninitialized` (`(sessionId: string) => void | Promise<void>`, optional): Callback for new session initialization.
    *   **`onsessionclosed`** (`(sessionId: string) => void | Promise<void>`, optional): Callback when a session is explicitly closed via `DELETE`.
    *   `enableJsonResponse` (`boolean`, optional): If `true`, non-streaming requests return JSON instead of establishing an SSE stream. Defaults to `false`.
    *   `eventStore` (`EventStore`, optional): An implementation of the `EventStore` interface to enable resumability.
    *   `allowedHosts` (`string[]`, optional): List of allowed `Host` header values for DNS rebinding protection.
    *   `allowedOrigins` (`string[]`, optional): List of allowed `Origin` header values for DNS rebinding protection.
    *   `enableDnsRebindingProtection` (`boolean`, optional): Enables DNS rebinding protection if `allowedHosts` or `allowedOrigins` are configured. Defaults to `false`.
    *   `retryInterval` (`number`, optional): Suggests a retry interval (in milliseconds) to clients in SSE priming events.

---

## Request Handling

### `handleRequest(req, res, parsedBody)`

Main entry point for handling incoming HTTP requests (GET, POST, DELETE). Must be integrated with your HTTP server's request handling logic.

```typescript
await httpTransport.handleRequest(req, res);
```

*   **`req`** (`IncomingMessage & { auth?: AuthInfo }`): The Node.js incoming request object.
*   **`res`** (`ServerResponse`): The Node.js server response object.
*   **`parsedBody`** (`unknown`, optional): The parsed body of the request, if already consumed by middleware.
*   **Returns**: `Promise<void>` - Resolves when the request is handled.

### `closeSSEStream(requestId)`

Closes an SSE stream associated with a specific request, typically used to implement polling behavior for long-running operations.

```typescript
httpTransport.closeSSEStream(requestId);
```

*   **`requestId`** (`RequestId`): The ID of the request stream to close.
