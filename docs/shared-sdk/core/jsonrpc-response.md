# JSONRPCResponse Interface

Represents a JSON-RPC 2.0 response message.

```typescript
interface JSONRPCResponse extends JSONRPCMessage {
  result?: unknown;
  error?: {
    code: number;
    message: string;
    data?: unknown;
  };
  id: number | string;
}
```

<h2>Properties</h2>

1.  `result` (`unknown`, optional): The value returned by the method.
2.  `error` (`object`, optional): An error object if an error occurred while invoking the method.
    1.  `code` (`number`): A number indicating the error type that occurred.
    2.  `message` (`string`): A string providing a short description of the error.
    3.  `data` (`unknown`, optional): A Primitive or Structured value that contains additional information about the error.
3.  `id` (`number | string`): This must be the same id as the Request object it is replying to.
