# MCP SDK Types Index

This document serves as a centralized index for TypeScript definitions across the Model Context Protocol (MCP) SDKs.

## Quick Links

- [**Client SDK Types**](docs/client-sdk/types.md): Definitions for building MCP Clients (Hosts/Agents).
- [**Server SDK Types**](docs/server-sdk/types.md): Definitions for building MCP Servers.

## Shared Data Models

The following types are core to the protocol and shared conceptually between Client and Server interactions.

### Protocol Messages

```typescript
// JSON-RPC 2.0 Base
interface JSONRPCMessage {
  jsonrpc: "2.0";
}

interface JSONRPCRequest extends JSONRPCMessage {
  method: string;
  params?: object;
  id?: number | string;
}

interface JSONRPCResponse extends JSONRPCMessage {
  result?: unknown;
  error?: {
    code: number;
    message: string;
    data?: unknown;
  };
  id: number | string;
}

interface JSONRPCNotification extends JSONRPCMessage {
  method: string;
  params?: object;
}
```

### Content Types

Used in Tools, Resources, Prompts, and Sampling.

```typescript
interface TextContent {
  type: "text";
  text: string;
}

interface ImageContent {
  type: "image";
  data: string; // Base64 encoded
  mimeType: string;
}

interface EmbeddedResource {
  type: "resource";
  resource: {
    uri: string;
    mimeType?: string;
    text?: string;
    blob?: string; // Base64 encoded
  };
}
```

## Architecture Overview

1.  **Transport Layer**: Handles raw message passing (Stdio, SSE).
2.  **Protocol Layer**: Manages JSON-RPC message framing and correlation.
3.  **Application Layer**:
    *   **Client**: `Client` class connects to transports, exposes `callTool`, `readResource`, etc.
    *   **Server**: `McpServer` class connects to transports, registers capabilities (`tool`, `resource`).
