# TypeScript MCP Client SDK

The Model Context Protocol (MCP) Client SDK for TypeScript allows you to build clients (often referred to as "Hosts" or "Agents") that connect to MCP servers to consume their Tools, Resources, and Prompts.

## Getting Started

1.  **[Quickstart](quickstart.md)**: Connect to a server and call a tool in minutes.
2.  **[Architecture](architecture.md)**: Understand the Client <-> Transport <-> Server relationship.

## API Reference

*   **[Client](api/client.md)**: The core class for connecting to servers and making requests.
*   **[Transports](api/transports.md)**: Index of available client transports.
*   **[StdioClientTransport](api/stdio-client-transport.md)**: Communicate with a server process via standard input/output.
*   **[StreamableHTTPClientTransport](api/streamable-http-client-transport.md)**: Connect via HTTP/SSE (Recommended).
*   **[SSEClientTransport](api/sse-client-transport.md)**: Legacy HTTP/SSE transport (Deprecated).

## Capabilities

Learn how to consume features exposed by MCP servers:

*   **[Tools](capabilities/tools.md)**: Discover and execute server-side functions.
*   **[Resources](capabilities/resources.md)**: Read data files and subscribe to updates.
*   **[Prompts](capabilities/prompts.md)**: Retrieve prompt templates.
*   **[Roots](capabilities/roots.md)**: Expose local filesystem roots to the server.
*   **[Sampling](capabilities/sampling.md)**: Allow the server to request completions from your LLM (Human-in-the-loop).

## Implementation Patterns

*   **[Host Application](patterns/host-application.md)**: Best practices for integrating the MCP Client into an IDE or Agent.
