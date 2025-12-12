# RequestContext Interface

Context provided to handlers for tools, resources, and prompts.

```typescript
interface RequestContext {
  request: JSONRPCRequest;
  signal: AbortSignal;
}
```

<h2>Properties</h2>

1.  `request` (`JSONRPCRequest`): The incoming JSON-RPC request.
2.  `signal` (`AbortSignal`): An AbortSignal that can be used to cancel ongoing operations if the client disconnects.
