# Tools Capability

Tools are executable functions that the server exposes to the client (and typically an LLM). They allow the model to interact with external systems, perform calculations, or take actions.

## Registering a Tool

Use `server.registerTool` to register a new tool. You define the tool's name, its input arguments schema (using Zod), and a callback that executes the logic.

### Signature

```typescript
server.registerTool(
  options: {
    name: string;
    description?: string;
    args: ZodRawShape;
  },
  callback: (args, extra) => CallToolResult | Promise<CallToolResult>
)
```

### Basic Example

```typescript
import { z } from "zod";

server.registerTool(
  {
    name: "calculate-sum",
    description: "Calculates the sum of two numbers",
    args: {
      a: z.number().describe("The first number"),
      b: z.number().describe("The second number")
    }
  },
  async ({ a, b }) => {
    return {
      content: [{
        type: "text",
        text: String(a + b)
      }]
    };
  }
);
```

---

## Input Schema (Zod)

The schema defines what arguments the tool accepts. The MCP SDK uses Zod to validate these arguments at runtime.

*   **Primitives**: `z.string()`, `z.number()`, `z.boolean()`
*   **Optional**: `.optional()`
*   **Descriptions**: `.describe()` is crucial for the LLM to understand what the argument does.

```typescript
server.registerTool(
  {
    name: "fetch-weather",
    description: "Fetch weather for a city",
    args: {
      city: z.string().describe("City name"),
      unit: z.enum(["celsius", "fahrenheit"]).default("celsius")
    }
  },
  async ({ city, unit }) => {
    // ... implementation
  }
);
```

---

## Return Type: `CallToolResult`

The callback must return an object conforming to `CallToolResult`.

```typescript
interface CallToolResult {
  content: (TextContent | ImageContent | EmbeddedResource)[];
  isError?: boolean;
}

interface TextContent {
  type: "text";
  text: string;
}

interface ImageContent {
  type: "image";
  data: string; // base64 encoded
  mimeType: string;
}
```

---

## Handy Tool Snippets

### 1. Error Handling

If a tool fails, you can return a result with `isError: true` to inform the model without crashing the interaction.

```typescript
server.registerTool(
  {
    name: "read-file",
    description: "Reads a file from the filesystem",
    args: { path: z.string() }
  },
  async ({ path }) => {
    try {
      const data = await fs.readFile(path, "utf-8");
      return {
        content: [{ type: "text", text: data }]
      };
    } catch (err) {
      return {
        isError: true,
        content: [{ 
          type: "text", 
          text: `Failed to read file: ${err.message}` 
        }]
      };
    }
  }
);
```

### 2. Returning Images

Tools can return images (e.g., generated charts, screenshots) as base64 data.

```typescript
server.registerTool(
  {
    name: "generate-chart",
    description: "Generates a chart from data",
    args: { data: z.array(z.number()) }
  },
  async ({ data }) => {
    const pngBuffer = await createChart(data);
    return {
      content: [{
        type: "image",
        data: pngBuffer.toString("base64"),
        mimeType: "image/png"
      }]
    };
  }
);
```

### 3. Long-Running Tasks (Progress)

*Note: While MCP supports progress notifications, standard tools typically await completion. For very long tasks, consider returning a status ID that the model can poll.*

```typescript
server.registerTool(
  {
    name: "deploy-app",
    description: "Deploys the app from a branch",
    args: { branch: z.string() }
  },
  async ({ branch }) => {
    const deploymentId = await startDeployment(branch);
    return {
      content: [{ 
        type: "text", 
        text: `Deployment started. ID: ${deploymentId}. Use 'check-status' to monitor.` 
      }]
    };
  }
);
```