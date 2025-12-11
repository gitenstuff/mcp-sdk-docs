# Consuming Prompts

Clients can retrieve prompt templates to use in their UI or internal logic.

## Listing Prompts

```typescript
const result = await client.listPrompts();
```

## Getting a Prompt

```typescript
const result = await client.getPrompt({
  name: "code-review",
  arguments: { code: "..." }
});

const messages = result.messages;
// Pass 'messages' to your LLM provider
```
