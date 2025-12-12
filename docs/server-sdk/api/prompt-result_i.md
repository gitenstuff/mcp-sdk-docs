# PromptResult Interface

Return type for prompt handlers.

```typescript
interface PromptResult {
  description?: string;
  messages: PromptMessage[];
}
```

<h2>Properties</h2>

1.  `description` (`string`, optional): A description of the instantiated prompt.
2.  `messages` (`PromptMessage[]`): An array of prompt messages.
