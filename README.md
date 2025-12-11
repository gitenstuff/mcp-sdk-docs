# Model Context Protocol (MCP) Documentation

This repository contains documentation for the Model Context Protocol (MCP), including both the Client and Server SDKs for TypeScript.

---

# TypeScript MCP Client SDK

The Model Context Protocol (MCP) Client SDK for TypeScript allows you to build clients (often referred to as "Hosts" or "Agents") that connect to MCP servers to consume their Tools, Resources, and Prompts.

## Getting Started

1.  **[Quickstart](docs/client-sdk/quickstart.md)**: Connect to a server and call a tool in minutes.
2.  **[Architecture](docs/client-sdk/architecture.md)**: Understand the Client <-> Transport <-> Server relationship.

## API Reference

*   **[Client](docs/client-sdk/api/client.md)**: The core class for connecting to servers and making requests.
*   **[Transports](docs/client-sdk/api/transports.md)**: `StdioClientTransport` (CLI) and `SSEClientTransport` (HTTP).
*   **[Types](docs/client-sdk/types.md)**: TypeScript definitions.

## Capabilities

Learn how to consume features exposed by MCP servers:

*   **[Tools](docs/client-sdk/capabilities/tools.md)**: Discover and execute server-side functions.
*   **[Resources](docs/client-sdk/capabilities/resources.md)**: Read data files and subscribe to updates.
*   **[Prompts](docs/client-sdk/capabilities/prompts.md)**: Retrieve prompt templates.
*   **[Roots](docs/client-sdk/capabilities/roots.md)**: Expose local filesystem roots to the server.
*   **[Sampling](docs/client-sdk/capabilities/sampling.md)**: Allow the server to request completions from your LLM (Human-in-the-loop).

## Implementation Patterns

*   **[Host Application](docs/client-sdk/patterns/host-application.md)**: Best practices for integrating the MCP Client into an IDE or Agent.

---

# TypeScript MCP Server SDK

The Model Context Protocol (MCP) Server SDK for TypeScript allows you to build servers that expose data (Resources), executable logic (Tools), and templates (Prompts) to LLMs and other clients.

## Getting Started

1.  **[Quickstart](docs/server-sdk/quickstart.md)**: Build your first server in under 5 minutes.
2.  **[Architecture](docs/server-sdk/architecture.md)**: Understand how the Transport, Protocol, and Server layers work together.

## API Reference

*   **[McpServer](docs/server-sdk/api/mcp-server.md)**: The core class for building servers.
*   **[Transports](docs/server-sdk/api/transports.md)**: `StdioServerTransport` (CLI) and `StreamableHTTPServerTransport` (HTTP/SSE).
*   **[Types](docs/server-sdk/types.md)**: TypeScript definitions.

## Capabilities

Learn how to implement the core features of MCP:

*   **[Tools](docs/server-sdk/capabilities/tools.md)**: Register executable functions with Zod validation.
*   **[Resources](docs/server-sdk/capabilities/resources.md)**: Expose file content or data via URIs.
*   **[Prompts](docs/server-sdk/capabilities/prompts.md)**: Create reusable prompt templates.
*   **[Sampling](docs/server-sdk/capabilities/sampling.md)**: Request completions ("thinking") from the client's LLM.

## Implementation Patterns

*   **[Authentication](docs/server-sdk/patterns/authentication.md)**: Secure your server using OAuth and Bearer tokens.
*   **[Error Handling](docs/server-sdk/patterns/error-handling.md)**: Use `McpError` and standard codes for robust failure modes.
