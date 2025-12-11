# Supporting Sampling

If your client is an agent or host with access to an LLM, you can allow the server to request completions.

## 1. Declare Capability

```typescript
const client = new Client(
  { ... },
  {
    capabilities: {
      sampling: {}
    }
  }
);
```

## 2. Handle Requests

Set a request handler for `sampling/createMessage`.

```typescript
import { CreateMessageRequestSchema } from "@modelcontextprotocol/sdk/types.js";

client.setRequestHandler(CreateMessageRequestSchema, async (request) => {
  const { messages, systemPrompt, maxTokens } = request.params;

  // Call your LLM here
  const llmResponse = await myLLM.generate({
    messages,
    system: systemPrompt,
    maxTokens
  });

  return {
    model: "gpt-4o",
    role: "assistant",
    content: {
      type: "text",
      text: llmResponse.text
    }
  };
});
```
