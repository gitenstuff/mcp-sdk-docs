# Sampling Capability

Sampling allows the **Server** to request a completion (generation) from the **Client's** LLM. This is useful for "human-in-the-loop" workflows where the server needs the LLM's help to process data, summarize content, or make a decision before proceeding.

> **Note:** Not all clients support sampling. The server should handle cases where the client denies the request.

## Requesting a Completion

Use `server.createMessage` (exposed via the `server.server` property on `McpServer`) to send a sampling request.

### Signature

```typescript
// Accessed via the underlying server instance
server.server.createMessage(
  request: CreateMessageRequestParams
)
```

### Basic Example

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";

const server = new McpServer({ name: "my-server", version: "1.0.0" });

// Inside a tool callback or other handler
server.registerTool(
  {
    name: "summarize-logs",
    args: {}
  },
  async (args, extra) => {
  
  // Request the client's LLM to summarize text
  const result = await server.server.createMessage({
    messages: [
      {
        role: "user",
        content: {
          type: "text", 
          text: "Here are the logs: [ERROR] DB connection failed... Summarize the issue." 
        }
      }
    ],
    maxTokens: 100,
    includeContext: "none" // Ask client NOT to include its full conversation history
  });

  return {
    content: [{
      type: "text",
      text: `LLM Summary: ${result.content.text}`
    }]
  };
});
```

---

## Configuration Options

The `CreateMessageRequestParams` object allows you to control the generation:

*   **`messages`**: The prompt to send to the LLM.
*   **`systemPrompt`**: (Optional) Instructions for the model.
*   **`includeContext`**: Controls privacy/cost.
    *   `"none"`: Send only the messages in this request.
    *   `"thisServer"`: Include history related to this server.
    *   `"allServers"`: Include full conversation history (client discretion).
*   **`temperature`**: Creativity (0.0 to 1.0).
*   **`maxTokens`**: Limit the response length.
*   **`stopSequences`**: Array of strings to stop generation.

### Example with Options

```typescript
const result = await server.server.createMessage({
  messages: [...],
  systemPrompt: "You are a secure code auditor.",
  includeContext: "none",
  temperature: 0.1,
  stopSequences: ["\n\n"]
});
```

---

## Return Type: `CreateMessageResult`

The result contains the generated content and the model name used by the client.

```typescript
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
  model: string; // The name of the model that generated the response
  stopReason?: "endTurn" | "stopSequence" | "maxTokens";
}
```

---

## Handy Sampling Snippets

### 1. Agentic Loop (Server-Side Logic)

The server can use the client's LLM to "think" about how to process a request.

```typescript
server.registerTool(
  {
    name: "complex-query",
    args: { query: z.string() }
  },
  async ({ query }) => {
  // 1. Ask LLM to break down the query
  const plan = await server.server.createMessage({
    messages: [{
      role: "user", 
      content: { type: "text", text: `Break down this query into SQL steps: ${query}` }
    }]
  });

  // 2. Execute the plan (pseudo-code)
  const results = await executeSqlSteps(plan.content.text);

  // 3. Ask LLM to format the answer
  const finalAnswer = await server.server.createMessage({
    messages: [{
      role: "user", 
      content: { type: "text", text: `Format these results: ${JSON.stringify(results)}` }
    }]
  });

  return {
    content: [{ type: "text", text: finalAnswer.content.text }]
  };
});
```

### 2. Content Filtering / Sanitization

Before saving data, ask the LLM to verify it.

```typescript
async function validateComment(comment: string) {
  const check = await server.server.createMessage({
    messages: [{
      role: "user",
      content: {
        type: "text", 
        text: `Is this comment offensive? Answer YES or NO.\n\n"${comment}"` 
      }
    }],
    maxTokens: 5
  });

  return check.content.text.trim().toUpperCase() === "NO";
}
```