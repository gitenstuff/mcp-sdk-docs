# ServerCapabilities Interface

Capability configuration for the server.

```typescript
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
```

<h2>Properties</h2>

1.  `logging` (`object`, optional): Configuration for logging capabilities.
2.  `resources` (`object`, optional): Resource-related capabilities.
    1.  `subscribe` (`boolean`, optional): Indicates if resource subscription is supported.
    2.  `listChanged` (`boolean`, optional): Indicates if listing changed resources is supported.
3.  `tools` (`object`, optional): Tool-related capabilities.
    1.  `listChanged` (`boolean`, optional): Indicates if listing changed tools is supported.
4.  `prompts` (`object`, optional): Prompt-related capabilities.
    1.  `listChanged` (`boolean`, optional): Indicates if listing changed prompts is supported.
5.  `experimental` (`Record<string, object>`, optional): Experimental capabilities.
