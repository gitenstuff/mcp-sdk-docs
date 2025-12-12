# ClientOptions Interface

Configuration options for the Client.

```typescript
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
```

## Properties

1.  `capabilities` (`object`, optional): Capabilities declared by this client.
    1.  `sampling` (`object`, optional): Configuration for sampling capability.
    2.  `roots` (`object`, optional): Configuration for roots capability.
    3.  `[key: string]` (`object | undefined`): Other capability-specific configuration objects.
