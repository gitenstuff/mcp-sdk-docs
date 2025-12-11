# Prompts Capability

Prompts allow servers to provide reusable prompt templates that clients can discover and use. These templates can include dynamic arguments, making them flexible for various contexts.

## Registering a Prompt

Use `server.prompt` to register a new prompt. You define the prompt's name, its argument schema (using Zod), and a callback that generates the prompt messages.

### Signature

```typescript
server.prompt(
  name: string,
  schema: ZodRawShapeCompat,
  callback: (args) => GetPromptResult | Promise<GetPromptResult>
)
```

### Basic Example

```typescript
import { z } from "zod";

server.prompt(
  "git-commit",
  {
    changes: z.string().describe("The git diff or description of changes")
  },
  async ({ changes }) => {
    return {
      messages: [
        {
          role: "user",
          content: {
            type: "text",
            text: `Generate a concise git commit message for the following changes:\n\n${changes}`
          }
        }
      ]
    };
  }
);
```

---

## Prompt Arguments

Arguments are defined using a Zod schema object. The keys of the object become the argument names.

*   **Required Arguments**: Use standard Zod types (e.g., `z.string()`).
*   **Optional Arguments**: Use `.optional()` (e.g., `z.string().optional()`).
*   **Descriptions**: Use `.describe()` to provide help text for the client.

```typescript
server.prompt(
  "explain-code",
  {
    code: z.string().describe("The code snippet to explain"),
    detailLevel: z.enum(["brief", "detailed"]).optional().describe("Level of detail")
  },
  async ({ code, detailLevel = "brief" }) => {
    const promptText = detailLevel === "brief" 
      ? `Briefly explain this code:\n${code}`
      : `Provide a detailed analysis of this code:\n${code}`;

    return {
      messages: [
        {
          role: "user",
          content: { type: "text", text: promptText }
        }
      ]
    };
  }
);
```

---

## Return Type: `GetPromptResult`

The callback must return an object conforming to `GetPromptResult`.

```typescript
interface GetPromptResult {
  description?: string;
  messages: PromptMessage[];
}

interface PromptMessage {
  role: "user" | "assistant";
  content: {
    type: "text";
    text: string;
  } | {
    type: "image";
    data: string; // base64 encoded
    mimeType: string;
  } | {
    type: "resource";
    resource: {
        uri: string;
        text?: string;
        blob?: string;
        mimeType?: string;
    };
  };
}
```

---

## Handy Prompt Snippets

Here are some common prompt patterns you can adapt:

### 1. Code Review

```typescript
server.prompt(
  "review-code",
  { code: z.string() },
  ({ code }) => ({
    messages: [{
      role: "user",
      content: {
        type: "text",
        text: `Please review the following code for bugs, security issues, and style violations:\n\n${code}`
      }
    }]
  })
);
```

### 2. Unit Test Generation

```typescript
server.prompt(
  "generate-tests",
  { 
    code: z.string(),
    framework: z.string().default("vitest") 
  },
  ({ code, framework }) => ({
    messages: [{
      role: "user",
      content: {
        type: "text",
        text: `Write unit tests for the following code using ${framework}. Ensure high coverage.\n\n${code}`
      }
    }]
  })
);
```

### 3. Debugging Assistant

```typescript
server.prompt(
  "debug-error",
  { 
    error: z.string(),
    context: z.string().optional() 
  },
  ({ error, context }) => ({
    messages: [{
      role: "user",
      content: {
        type: "text",
        text: `I'm encountering the following error:\n${error}\n\n${context ? `Context:\n${context}` : ''}\n\nAnalyze the cause and suggest a fix.`
      }
    }]
  })
);
```