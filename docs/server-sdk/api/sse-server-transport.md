# SSEServerTransport Class API (Deprecated)

**Deprecated:** This class is deprecated and should not be used for new development. Use `StreamableHTTPServerTransport` instead.

The `SSEServerTransport` facilitates communication over HTTP using Server-Sent Events (SSE). It is maintained for compatibility with older protocol versions.

## Index

*   [Constructor](#constructor)
*   [Methods](#methods)
    *   [start](#start)
    *   [handlePostMessage](#handlepostmessagereq-res-parsedbody)

## Constructor

```typescript
import { SSEServerTransport } from '@modelcontextprotocol/sdk/server/sse.js';

const transport = new SSEServerTransport("/sse", res);
```

### Parameters
1.  **`path`** (`string`): The path where the SSE stream is served.
2.  **`res`** (`ServerResponse`): The Node.js server response object for the initial SSE connection.

---

## Methods

### `start()`

Starts the SSE stream.

```typescript
await transport.start();
```

*   **Returns**: `Promise<void>`

### `handlePostMessage(req, res, parsedBody)`

Handles incoming POST messages (JSON-RPC requests) associated with the SSE session.

```typescript
await transport.handlePostMessage(req, res);
```

*   **`req`** (`IncomingMessage`): The incoming HTTP request.
*   **`res`** (`ServerResponse`): The server response object.
*   **`parsedBody`** (`unknown`, optional): The parsed body of the request.
*   **Returns**: `Promise<void>`
