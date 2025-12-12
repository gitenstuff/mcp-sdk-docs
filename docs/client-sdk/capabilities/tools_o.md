# Consuming Tools

Clients can discover and execute tools exposed by the server.

## Listing Tools

```typescript
const result = await client.listTools();
// result.tools is an array of Tool objects
```

## Calling Tools

The `Client` class validates tool output against the server's provided schema automatically.

```typescript
const result = await client.callTool({
  name: "weather",
  arguments: { city: "San Francisco" }
});

// result.content contains the tool output
```
