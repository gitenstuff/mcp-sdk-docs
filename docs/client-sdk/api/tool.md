# Tool Interface

Represents a tool available on the server.

```typescript
interface Tool {
  name: string;
  description?: string;
  inputSchema: object; // JSON Schema definition
}
```

## Properties

1.  `name` (`string`): The name of the tool.
2.  `description` (`string`, optional): A description of what the tool does.
3.  `inputSchema` (`object`): JSON Schema definition for the tool's input arguments.
