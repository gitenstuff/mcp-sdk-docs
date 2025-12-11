# Client SDK TypeScript Definitions

## Client Class

```typescript
/**
 * Metadata about the client implementation.
 */
interface ClientImplementation {
  name: string;
  version: string;
}

/**
 * Configuration options for the Client.
 */
interface ClientOptions {
  /**
   * Capabilities declared by this client.
   * Keys are capability names (e.g., "sampling", "roots").
   * Values are capability-specific configuration objects.
   */
  capabilities?: {
    sampling?: object;
    roots?: object;
    [key: string]: object | undefined;
  };
}

/**
 * The primary class for connecting to and interacting with MCP servers.
 */
declare class Client {
  constructor(clientInfo: ClientImplementation, options?: ClientOptions);

  /**
   * Connects to the server via the provided transport.
   * Performs protocol version negotiation and capability exchange.
   */
  connect(transport: Transport): Promise<void>;

  /**
   * Closes the connection to the server.
   */
  close(): Promise<void>;

  /**
   * Discovers available tools on the server.
   */
  listTools(): Promise<{ tools: Tool[] }>;

  /**
   * Executes a tool on the server.
   */
  callTool(params: {
    name: string;
    arguments: Record<string, unknown>;
  }): Promise<CallToolResult>;

  /**
   * Discovers available resources on the server.
   */
  listResources(): Promise<{ resources: Resource[] }>;

  /**
   * Reads the content of a specific resource.
   */
  readResource(params: { uri: string }): Promise<ReadResourceResult>;

  /**
   * Discovers available prompts on the server.
   */
  listPrompts(): Promise<{ prompts: Prompt[] }>;

  /**
   * Retrieves a prompt template instantiated with arguments.
   */
  getPrompt(params: {
    name: string;
    arguments?: Record<string, string>;
  }): Promise<GetPromptResult>;
}
```

## Transport Interfaces

```typescript
/**
 * Abstract interface for MCP transports.
 */
interface Transport {
  start(): Promise<void>;
  close(): Promise<void>;
  send(message: JSONRPCMessage): Promise<void>;
  onmessage?: (message: JSONRPCMessage) => void;
  onclose?: () => void;
  onerror?: (error: Error) => void;
}

/**
 * Options for StdioClientTransport.
 */
interface StdioClientTransportOptions {
  command: string;           // Executable path
  args?: string[];           // Command line arguments
  env?: NodeJS.ProcessEnv;   // Environment variables
  stderr?: "inherit" | "ignore";
}

declare class StdioClientTransport implements Transport {
  constructor(options: StdioClientTransportOptions);
}

/**
 * Modern HTTP transport for Client.
 */
declare class StreamableHTTPClientTransport implements Transport {
  constructor(url: URL, options?: {
    authProvider?: object; // OAuthClientProvider
    requestInit?: RequestInit;
    fetch?: unknown; // FetchLike
    reconnectionOptions?: object;
    sessionId?: string;
  });
}

/**
 * @deprecated Use StreamableHTTPClientTransport instead.
 */
declare class SSEClientTransport implements Transport {
  constructor(url: URL, options?: {
    eventSourceInit?: EventSourceInit;
    requestInit?: RequestInit;
  });
}
```

## Data Models

```typescript
/**
 * Represents a tool available on the server.
 */
interface Tool {
  name: string;
  description?: string;
  inputSchema: object; // JSON Schema definition
}

/**
 * Result of a tool execution.
 */
interface CallToolResult {
  content: (TextContent | ImageContent | EmbeddedResource)[];
  isError?: boolean;
}

/**
 * Represents a resource available on the server.
 */
interface Resource {
  uri: string;
  name: string;
  description?: string;
  mimeType?: string;
}

/**
 * Result of reading a resource.
 */
interface ReadResourceResult {
  contents: (TextResourceContents | BlobResourceContents)[];
}

interface TextResourceContents {
  uri: string;
  mimeType?: string;
  text: string;
}

interface BlobResourceContents {
  uri: string;
  mimeType?: string;
  blob: string; // Base64 encoded data
}

/**
 * Represents a prompt template.
 */
interface Prompt {
  name: string;
  description?: string;
  arguments?: {
    name: string;
    description?: string;
    required?: boolean;
  }[];
}

/**
 * Result of instantiating a prompt.
 */
interface GetPromptResult {
  description?: string;
  messages: PromptMessage[];
}

interface PromptMessage {
  role: "user" | "assistant";
  content: {
    type: "text";
    text: string;
  } | {
    type: "image";
    data: string;
    mimeType: string;
  } | {
    type: "resource";
    resource: {
        uri: string;
        text?: string;
        blob?: string;
        mimeType?: string;
    };
  };
}

// Content Types
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
    blob?: string;
  };
}
```
