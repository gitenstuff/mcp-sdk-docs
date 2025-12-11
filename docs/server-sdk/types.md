# Server SDK TypeScript Definitions

## McpServer Class

```typescript
/**
 * Metadata about the server implementation.
 */
interface ServerImplementation {
  name: string;
  version: string;
}

/**
 * Capability configuration for the server.
 */
interface ServerCapabilities {
  logging?: object;
  resources?: {
    subscribe?: boolean;
    listChanged?: boolean;
  };
  tools?: {
    listChanged?: boolean;
  };
  prompts?: {
    listChanged?: boolean;
  };
  experimental?: Record<string, object>;
}

/**
 * Options for initializing McpServer.
 */
interface ServerOptions {
  capabilities?: ServerCapabilities;
  enforceStrictCapabilities?: boolean;
}

/**
 * The high-level class for building MCP servers.
 */
declare class McpServer {
  constructor(serverInfo: ServerImplementation, options?: ServerOptions);

  /**
   * Connects the server to a transport.
   */
  connect(transport: Transport): Promise<void>;

  /**
   * Closes the server connection.
   */
  close(): Promise<void>;

  /**
   * Registers a tool.
   */
  tool<Schema extends ZodRawShape>(
    name: string,
    description: string,
    parameters: Schema,
    handler: (args: z.infer<ZodObject<Schema>>, extra: RequestContext) => ToolResult | Promise<ToolResult>
  ): void;
  
  // Overload for tool without description
  tool<Schema extends ZodRawShape>(
    name: string,
    parameters: Schema,
    handler: (args: z.infer<ZodObject<Schema>>, extra: RequestContext) => ToolResult | Promise<ToolResult>
  ): void;

  /**
   * Registers a resource.
   */
  resource(
    name: string,
    uriOrTemplate: string,
    handler: (uri: URL, variables: Record<string, string>, extra: RequestContext) => ResourceResult | Promise<ResourceResult>
  ): void;

  /**
   * Registers a prompt.
   */
  prompt<Schema extends ZodRawShape>(
    name: string,
    description: string,
    parameters: Schema,
    handler: (args: z.infer<ZodObject<Schema>>) => PromptResult | Promise<PromptResult>
  ): void;
    
   // Overload for prompt without description
  prompt<Schema extends ZodRawShape>(
    name: string,
    parameters: Schema,
    handler: (args: z.infer<ZodObject<Schema>>) => PromptResult | Promise<PromptResult>
  ): void;

  /**
   * Access to the low-level Server instance.
   */
  server: Server;
}

/**
 * Context provided to handlers.
 */
interface RequestContext {
  request: JSONRPCRequest;
  signal: AbortSignal;
}
```

## Transport Interfaces

```typescript
declare class StdioServerTransport implements Transport {
  constructor(stdin?: ReadableStream, stdout?: WritableStream);
}

interface StreamableHTTPServerTransportOptions {
  sessionIdGenerator?: () => string | undefined;
  enableJsonResponse?: boolean;
  enableDnsRebindingProtection?: boolean;
  allowedHosts?: string[];
  allowedOrigins?: string[];
  onsessioninitialized?: (sessionId: string) => void;
  onsessionclosed?: (sessionId: string) => void;
}

declare class StreamableHTTPServerTransport implements Transport {
  constructor(options?: StreamableHTTPServerTransportOptions);
  
  handleRequest(
    req: IncomingMessage, 
    res: ServerResponse, 
    parsedBody?: unknown
  ): Promise<void>;
}

/**
 * @deprecated Use StreamableHTTPServerTransport instead.
 */
declare class SSEServerTransport implements Transport {
  constructor(path: string, res: ServerResponse);
  start(): Promise<void>;
  handlePostMessage(req: IncomingMessage, res: ServerResponse, parsedBody?: unknown): Promise<void>;
}
```

## Handler Return Types

```typescript
/**
 * Return type for tool handlers.
 */
interface ToolResult {
  content: (TextContent | ImageContent | EmbeddedResource)[];
  isError?: boolean;
}

/**
 * Return type for resource handlers.
 */
interface ResourceResult {
  contents: (TextResourceContents | BlobResourceContents)[];
}

/**
 * Return type for prompt handlers.
 */
interface PromptResult {
  description?: string;
  messages: PromptMessage[];
}
```

## Sampling (Client Request)

```typescript
/**
 * Methods available on server.server for requesting sampling.
 */
interface Server {
  createMessage(params: CreateMessageRequestParams): Promise<CreateMessageResult>;
}

interface CreateMessageRequestParams {
  messages: SamplingMessage[];
  systemPrompt?: string;
  includeContext?: "none" | "thisServer" | "allServers";
  temperature?: number;
  maxTokens?: number;
  stopSequences?: string[];
  metadata?: Record<string, unknown>;
  modelPreferences?: {
    hints?: { name: string }[];
    costPriority?: number;
    speedPriority?: number;
    intelligencePriority?: number;
  };
}

interface CreateMessageResult {
  role: "assistant";
  content: {
    type: "text";
    text: string;
  } | {
    type: "image";
    data: string;
    mimeType: string;
  };
  model: string;
  stopReason?: "endTurn" | "stopSequence" | "maxTokens";
}

interface SamplingMessage {
  role: "user" | "assistant";
  content: {
      type: "text";
      text: string;
  } | {
      type: "image";
      data: string;
      mimeType: string;
  };
}
```
