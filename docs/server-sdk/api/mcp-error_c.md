# McpError Class API

The `McpError` class extends the standard JavaScript `Error` class, adding a numeric `code` and optional `data` field. It is the standard way to throw protocol-aware errors from your tools, resources, and prompt handlers.

## Index

*   [Constructor](#constructor)

## Constructor

```typescript
import { McpError, ErrorCode } from "@modelcontextprotocol/sdk/types.js";

throw new McpError(
  ErrorCode.InvalidParams,
  "User ID not found"
);
```

### Parameters
1.  **`code`** (`ErrorCode | number`): A numeric error code.
2.  **`message`** (`string`): A human-readable description of the error.
3.  **`data`** (`unknown`, optional): Additional structured data to help the client debug or handle the error.
