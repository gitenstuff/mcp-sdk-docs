# TypeScript MCP Server SDK

The Model Context Protocol (MCP) Server SDK for TypeScript allows you to build servers that expose data (Resources), executable logic (Tools), and templates (Prompts) to LLMs and other clients.

## Getting Started

1.  **[Quickstart](quickstart.md)**: Build your first server in under 5 minutes.
2.  **[Architecture](architecture.md)**: Understand how the Transport, Protocol, and Server layers work together.

## API Reference

*   **[McpServer](api/mcp-server.md)**: The core class for building servers.
*   **[Transports](api/transports.md)**: `StdioServerTransport` (CLI) and `StreamableHTTPServerTransport` (HTTP/SSE).

## Capabilities

Learn how to implement the core features of MCP:

*   **[Tools](capabilities/tools.md)**: Register executable functions with Zod validation.
*   **[Resources](capabilities/resources.md)**: Expose file content or data via URIs.
*   **[Prompts](capabilities/prompts.md)**: Create reusable prompt templates.
*   **[Sampling](capabilities/sampling.md)**: Request completions ("thinking") from the client's LLM.

## Implementation Patterns

*   **[Authentication](patterns/authentication.md)**: Secure your server using OAuth and Bearer tokens.
*   **[Error Handling](patterns/error-handling.md)**: Use `McpError` and standard codes for robust failure modes.