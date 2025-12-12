# JSONRPCNotification Interface

Represents a JSON-RPC 2.0 notification message.

```typescript
interface JSONRPCNotification extends JSONRPCMessage {
  method: string;
  params?: object;
}
```

## Properties

1.  `method` (`string`): The name of the method to be invoked.
2.  `params` (`object`, optional): A Structured value that holds the parameter values to be used during the invocation of the method.
