# Transports API

Transports define how the `McpServer` communicates with clients. The Model Context Protocol (MCP) supports various transport mechanisms to suit different application architectures.

## `StdioServerTransport`

The `StdioServerTransport` facilitates communication over standard input/output (stdin/stdout). This transport is ideal for command-line interface (CLI) applications and scenarios where the server and client are co-located within the same process or communicate via piped streams.

### When to Use

*   CLI tools.
*   Inter-process communication within a single machine using standard streams.
*   Simple, local server implementations.

### Instantiation

```typescript
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';
import process from 'node:process';

// Basic usage, defaults to process.stdin and process.stdout
const stdioTransport = new StdioServerTransport();

// Or with custom streams
// const customStdin = new MyReadableStream();
// const customStdout = new MyWritableStream();
// const stdioTransport = new StdioServerTransport(customStdin, customStdout);

const server = new McpServer({ name: 'my-cli-server', version: '1.0.0' });
server.connect(stdioTransport);
```

### Constructor

`new StdioServerTransport(stdin?: Readable, stdout?: Writable)`

*   **`stdin`** (`Readable`, optional): The readable stream to use as input. Defaults to `process.stdin`.
*   **`stdout`** (`Writable`, optional): The writable stream to use as output. Defaults to `process.stdout`.

---

## `StreamableHTTPServerTransport`

The `StreamableHTTPServerTransport` is the recommended HTTP transport, designed for modern web-based communication. It supports Server-Sent Events (SSE) for streaming responses, direct JSON responses, and resumability for robust client connections. It is highly configurable to handle various network and security requirements.

### When to Use

*   Web applications.
*   Client-server architectures over HTTP/HTTPS.
*   When SSE streaming, direct JSON responses, or session resumability are required.
*   When advanced security features like DNS rebinding protection are needed.

### Instantiation and Integration

This transport requires integration with an existing Node.js HTTP server framework (e.g., Express.js).

```typescript
import { StreamableHTTPServerTransport } from '@modelcontextprotocol/sdk/server/streamableHttp.js';
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';
import { randomUUID } from 'node:crypto';
import http from 'node:http';

const httpTransport = new StreamableHTTPServerTransport({
  // Enables stateful session management
  sessionIdGenerator: () => randomUUID(),
  // Optional: allows direct JSON responses for non-streaming requests
  enableJsonResponse: true,
  // Optional: Enable resumability with an event store implementation
  // eventStore: new MyInMemoryEventStore(),
  // Optional: DNS rebinding protection
  allowedHosts: ['localhost:3000'],
  enableDnsRebindingProtection: true,
  onsessioninitialized: (sessionId) => console.log(`Session initialized: ${sessionId}`),
});

const server = new McpServer({ name: 'my-http-server', version: '1.0.0' });
server.connect(httpTransport);

// Integrate with an HTTP server (e.g., using Node's http module directly)
const httpServer = http.createServer(async (req, res) => {
  // Pass all incoming requests to the transport's handler
  await httpTransport.handleRequest(req, res);
});

httpServer.listen(3000, () => {
  console.log('HTTP Server listening on port 3000');
});
```

### Constructor

`new StreamableHTTPServerTransport(options: StreamableHTTPServerTransportOptions)`

*   **`options`** (`StreamableHTTPServerTransportOptions`):
    *   **`sessionIdGenerator`** (`() => string | undefined`): A function to generate unique session IDs. Return `undefined` for stateless operation.
    *   **`onsessioninitialized`** (`(sessionId: string) => void | Promise<void>`): Callback for new session initialization.
    *   **`onsessionclosed`** (`(sessionId: string) => void | Promise<void>`): Callback when a session is explicitly closed via `DELETE`.
    *   **`enableJsonResponse`** (`boolean`, optional): If `true`, non-streaming requests return JSON instead of establishing an SSE stream. Defaults to `false`.
    *   **`eventStore`** (`EventStore`, optional): An implementation of the `EventStore` interface to enable resumability (allowing clients to reconnect and receive missed messages).
    *   **`allowedHosts`** (`string[]`, optional): List of allowed `Host` header values for DNS rebinding protection.
    *   **`allowedOrigins`** (`string[]`, optional): List of allowed `Origin` header values for DNS rebinding protection.
    *   **`enableDnsRebindingProtection`** (`boolean`, optional): Enables DNS rebinding protection if `allowedHosts` or `allowedOrigins` are configured. Defaults to `false`.
    *   **`retryInterval`** (`number`, optional): Suggests a retry interval (in milliseconds) to clients in SSE priming events.

### Methods

*   **`handleRequest(req: IncomingMessage & { auth?: AuthInfo }, res: ServerResponse, parsedBody?: unknown)`**:
    *   Main entry point for handling incoming HTTP requests (GET, POST, DELETE).
    *   Must be integrated with your HTTP server's request handling logic.
*   **`closeSSEStream(requestId: RequestId)`**: Closes an SSE stream associated with a specific request, typically used to implement polling behavior for long-running operations.

---

## `SSEServerTransport` (Deprecated)

This transport is **deprecated** and should not be used for new development. It is maintained for compatibility with older protocol versions. For new projects requiring HTTP communication, use `StreamableHTTPServerTransport`.

### When to Use (Legacy)

*   Maintaining compatibility with existing systems that rely on older SSE transport implementations.

### Deprecation Notice

`SSEServerTransport` lacks the advanced features and flexibility of `StreamableHTTPServerTransport`, including robust session management, explicit resumability, and integrated security options.
