# Architecture

```text
      [ Client / Host ]
             ↕
=============================
|       MCP Client          |
|                           |
|    [ Client Layer ]       |
|        (Client)           |
|            ↕              |
|   [ Protocol Layer ]      |
|    (JSON-RPC 2.0)         |
|            ↕              |
|  [ Transport Layer ]      |
|     (Stdio / SSE)         |
=============================
             ↕
      [ MCP Server ]
```

## 1. Transport Layer
**Responsibility:** Handles communication with the server.

*   **`StdioClientTransport`**: Spawns a server process as a child and communicates via stdin/stdout. Ideal for local tools.
*   **`SSEClientTransport`**: Connects to a server via HTTP (Server-Sent Events). Ideal for remote servers.

## 2. Protocol Layer (`Client`)
**Responsibility:** Implements JSON-RPC 2.0 and the MCP lifecycle.

*   **Handshake**: Sends `initialize` and `notifications/initialized`.
*   **Routing**: Matches responses to requests using IDs.
*   **Capabilities**: Negotiates what features (e.g., Sampling, Roots) this client supports.

## 3. Client Layer
**Responsibility:** Developer API.

The `Client` class provides type-safe methods to interact with the server:
*   `callTool()`
*   `readResource()`
*   `listPrompts()`

It also manages validation (e.g., validating tool outputs against their schemas).

## Connection Lifecycle

1.  **Instantiation**: Create a `Client` and a `Transport`.
2.  **Connection**: `client.connect(transport)` initiates the connection and performs the handshake.
3.  **Operation**: The client is ready to send requests (`callTool`, etc.).
