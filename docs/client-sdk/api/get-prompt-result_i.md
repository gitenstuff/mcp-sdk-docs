# GetPromptResult Interface

Represents the result of instantiating a prompt.

```typescript
interface GetPromptResult {
  description?: string;
  messages: PromptMessage[];
}
```

## Properties

1.  `description` (`string`, optional): A description of the instantiated prompt.
2.  `messages` (`PromptMessage[]`): An array of prompt messages to send to an LLM.
