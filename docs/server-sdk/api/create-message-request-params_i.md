# CreateMessageRequestParams Interface

Parameters for requesting the client's LLM to create a message.

```typescript
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
```

<h2>Properties</h2>

1.  `messages` (`SamplingMessage[]`): An array of messages to send to the LLM.
2.  `systemPrompt` (`string`, optional): A system-level prompt for the LLM.
3.  `includeContext` (`"none" | "thisServer" | "allServers"`, optional): Specifies whether to include context from the current or all servers.
4.  `temperature` (`number`, optional): Controls the randomness of the output.
5.  `maxTokens` (`number`, optional): The maximum number of tokens to generate.
6.  `stopSequences` (`string[]`, optional): Sequences that will stop the generation.
7.  `metadata` (`Record<string, unknown>`, optional): Arbitrary metadata to pass to the LLM.
8.  `modelPreferences` (`object`, optional): Preferences for the LLM model.
    1.  `hints` (`object[]`, optional): Hints for specific models.
    2.  `costPriority` (`number`, optional): Priority for cost optimization.
    3.  `speedPriority` (`number`, optional): Priority for speed optimization.
    4.  `intelligencePriority` (`number`, optional): Priority for intelligence optimization.
