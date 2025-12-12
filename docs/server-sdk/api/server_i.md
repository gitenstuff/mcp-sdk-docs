# Server Interface

Methods available on `server.server` for requesting sampling from the client's LLM.

```typescript
interface Server {
  createMessage(params: CreateMessageRequestParams): Promise<CreateMessageResult>;
}
```

## Methods

1.  `createMessage(params: CreateMessageRequestParams)` (`Promise<CreateMessageResult>`): Requests the client's LLM to create a message based on the provided parameters.
