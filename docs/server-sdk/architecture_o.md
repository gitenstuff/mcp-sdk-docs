# Architecture

The TypeScript SDK is built in layers, allowing for flexibility in how you build and deploy MCP servers.

```text
      [ Client / LLM ]
             ↕
=============================
|       MCP Server          |
|                           |
|  [ Transport Layer ]      |
|     (Stdio / HTTP)        |
|            ↕              |
|   [ Protocol Layer ]      |
|    (JSON-RPC 2.0)         |
|            ↕              |
|    [ Server Layer ]       |
|      (McpServer)          |
|            │              |
|            ▼              |
|    [ Capabilities ]       |
|   • Tools (Functions)     |
|   • Resources (Data)      |
|   • Prompts (Templates)   |
=============================
```

## 1. Transport Layer
**Responsibility:** Handles the raw bits and bytes of communication.

The transport layer is agnostic to the message content. It simply moves JSON-RPC messages between the client and the server.
*   **`StdioServerTransport`**: Reads from `stdin`, writes to `stdout`. Used for local process integration.
*   **`StreamableHTTPServerTransport`**: Handles HTTP POST (messages) and GET (SSE streams). Used for remote/web integration.

## 2. Protocol Layer (`Server`)
**Responsibility:** Implements JSON-RPC 2.0 and the MCP lifecycle.

This layer (exposed via the `Server` class) handles:
*   **Lifecycle**: Manages the connection state (Connected -> Initializing -> Ready).
*   **Negotiation**: Handshakes protocol versions and capabilities (`InitializeRequest`).
*   **Routing**: Dispatches JSON-RPC messages (Requests, Notifications, Errors) to the correct handlers.

*Most users will not interact directly with this layer unless they are building custom integrations.*

## 3. Server Layer (`McpServer`)
**Responsibility:** Developer Experience and Capability Management.

The `McpServer` class wraps the Protocol layer to provide a high-level, type-safe API.
*   **Registry**: Manages the list of available Tools, Resources, and Prompts.
*   **Validation**: Uses Zod to automatically validate incoming arguments for tools and prompts.
*   **Abstraction**: Hides the complexity of JSON-RPC message construction.

## Data Flow Example: Tool Call

1.  **Transport**: Receives a JSON string via HTTP POST. Parses it into a JSON object.
2.  **Protocol**: Identifies it as a `CallToolRequest` (JSON-RPC method `tools/call`).
3.  **Server**: 
    *   Looks up the tool by name.
    *   Validates the `arguments` using the registered Zod schema.
    *   Invokes your `callback` function.
    *   Wraps your return value in a `CallToolResult`.
4.  **Protocol**: Wraps the result in a JSON-RPC Response.
5.  **Transport**: Serializes the response and sends it back (via HTTP Response or SSE).

## Component Lifecycle

1.  **Instantiation**: You create an `McpServer` and a `Transport`.
2.  **Connection**: `server.connect(transport)` binds them together.
3.  **Initialization**: The client sends an `initialize` request. The `Server` layer handles this automatically, reporting the capabilities you registered.
4.  **Operation**: The server is now ready to handle requests (tools, resources) or send notifications (logging, sampling).
