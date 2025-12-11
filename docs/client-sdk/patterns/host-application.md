# Host Application Patterns

When integrating the MCP Client into a larger application (like an IDE or Agent), consider these patterns.

## Connection Management

*   **Lazy Connection**: Connect to the MCP server only when the user performs an action that requires it.
*   **Auto-Reconnect**: For HTTP transports, handle network drops gracefully. For Stdio, monitor the process exit code and restart if necessary.

## Capability Negotiation

Check `client.getServerCapabilities()` after connection to adapt your UI.

```typescript
await client.connect(transport);
const caps = client.getServerCapabilities();

if (caps.resources?.subscribe) {
  enableSubscribeButton();
}
```

## Security

*   **Stdio**: You are spawning a subprocess. Ensure you trust the executable.
*   **Sampling**: Always prompt the user for confirmation before sending data back to the server via sampling, or sandbox the server execution.
