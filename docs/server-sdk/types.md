# Server SDK TypeScript Definitions

This document indexes the TypeScript definitions relevant to the MCP Server SDK.

## McpServer Class Related

1.  **[ServerImplementation](api/server-implementation.md)**: Metadata about the server implementation.
2.  **[ServerCapabilities](api/server-capabilities.md)**: Capability configuration for the server.
3.  **[ServerOptions](api/server-options.md)**: Options for initializing `McpServer`.
4.  **[RequestContext](api/request-context.md)**: Context provided to handlers.

<h2>Transport Interfaces</h2>

1.  **[StreamableHTTPServerTransportOptions](api/streamable-http-server-transport-options.md)**: Options for `StreamableHTTPServerTransport`.

<h2>Handler Return Types</h2>

1.  **[ToolResult](api/tool-result.md)**: Return type for tool handlers.
2.  **[ResourceResult](api/resource-result.md)**: Return type for resource handlers.
3.  **[PromptResult](api/prompt-result.md)**: Return type for prompt handlers.

<h2>Sampling (Client Request)</h2>

1.  **[Server](api/server.md)**: Interface for requesting sampling from the client.
2.  **[CreateMessageRequestParams](api/create-message-request-params.md)**: Parameters for requesting a message creation.
3.  **[CreateMessageResult](api/create-message-result.md)**: Result of creating a message.
4.  **[SamplingMessage](api/sampling-message.md)**: A message used in sampling requests.

