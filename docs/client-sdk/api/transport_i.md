# Transport Interface

Abstract interface for MCP transports.

```typescript
interface Transport {
  start(): Promise<void>;
  close(): Promise<void>;
  send(message: JSONRPCMessage): Promise<void>;
  onmessage?: (message: JSONRPCMessage) => void;
  onclose?: () => void;
  onerror?: (error: Error) => void;
}
```

## Properties

1.  `start()` (`Promise<void>`): Starts the transport connection.
2.  `close()` (`Promise<void>`): Closes the transport connection.
3.  `send(message: JSONRPCMessage)` (`Promise<void>`): Sends a JSON-RPC message.
4.  `onmessage?` (`(message: JSONRPCMessage) => void`): Optional callback for incoming messages.
5.  `onclose?` (`() => void`): Optional callback for when the transport closes.
6.  `onerror?` (`(error: Error) => void`): Optional callback for transport errors.
