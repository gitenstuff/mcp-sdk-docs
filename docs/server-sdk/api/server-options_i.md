# ServerOptions Interface

Options for initializing `McpServer`.

```typescript
interface ServerOptions {
  capabilities?: ServerCapabilities;
  enforceStrictCapabilities?: boolean;
}
```

## Properties

1.  `capabilities` (`ServerCapabilities`, optional): Configures the capabilities of the server.
2.  `enforceStrictCapabilities` (`boolean`, optional): If true, capabilities not explicitly declared will be rejected.
