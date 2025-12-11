# Exposing Roots

Clients can expose "roots" (filesystem starting points) to the server, allowing the server to request permission to access specific directories.

## 1. Declare Capability

```typescript
const client = new Client(
  { ... },
  {
    capabilities: {
      roots: { listChanged: true }
    }
  }
);
```

## 2. Handle List Requests

```typescript
import { ListRootsRequestSchema } from "@modelcontextprotocol/sdk/types.js";

client.setRequestHandler(ListRootsRequestSchema, async () => {
  return {
    roots: [
      {
        uri: "file:///home/user/project",
        name: "Project Root"
      }
    ]
  };
});
```
