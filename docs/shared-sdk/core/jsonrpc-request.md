# JSONRPCRequest Interface

Represents a JSON-RPC 2.0 request message.

```typescript
interface JSONRPCRequest extends JSONRPCMessage {
  method: string;
  params?: object;
  id?: number | string;
}
```

## Properties

1.  `method` (`string`): The name of the method to be invoked.
2.  `params` (`object`, optional): A Structured value that holds the parameter values to be used during the invocation of the method.
3.  `id` (`number | string`, optional): An identifier established by the Client that MUST contain a String, Number, or NULL value if included.
