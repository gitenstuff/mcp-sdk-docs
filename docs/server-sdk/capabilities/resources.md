# Resources Capability

Resources allow servers to expose data to clients, such as files, database records, or system logs. Resources are identified by URIs and can be static (fixed URI) or dynamic (URI templates).

## Registering a Resource

Use `server.registerResource` to register a new resource. You define the resource's name, its URI (or URI template), and a callback that retrieves the content.

### Signature

```typescript
server.registerResource(
  options: {
    name: string;
    uri?: string;
    template?: string;
    mimeType?: string;
  },
  callback: (uri, variables) => ReadResourceResult | Promise<ReadResourceResult>
)
```

### Static Resource Example

Static resources have a fixed URI. This is useful for single files or global configuration.

```typescript
server.registerResource(
  {
    name: "system-config",
    uri: "file:///etc/myapp/config.json",
    mimeType: "application/json"
  },
  async (uri) => {
    const config = await readConfigFile();
    return {
      contents: [{
        uri: uri.href,
        mimeType: "application/json",
        text: JSON.stringify(config)
      }]
    };
  }
);
```

---

## Dynamic Resources (URI Templates)

URI Templates allow you to define a pattern that matches multiple resources. The server extracts variables from the URI which are passed to your callback.

### Template Syntax

Templates follow RFC 6570 Level 1 (e.g., `file:///logs/{id}`).

```typescript
server.registerResource(
  {
    name: "log-file",
    template: "file:///logs/{id}",
    mimeType: "text/plain"
  },
  async (uri, { id }) => {
    // If client requests "file:///logs/123", id will be "123"
    const logData = await getLogById(id);
    return {
      contents: [{
        uri: uri.href,
        mimeType: "text/plain",
        text: logData
      }]
    };
  }
);
```

---

## Return Type: `ReadResourceResult`

The callback must return an object conforming to `ReadResourceResult`.

```typescript
interface ReadResourceResult {
  contents: ResourceContent[];
}

type ResourceContent = 
  | TextResourceContents 
  | BlobResourceContents;

interface TextResourceContents {
  uri: string;
  mimeType?: string;
  text: string;
}

interface BlobResourceContents {
  uri: string;
  mimeType?: string;
  blob: string; // base64 encoded
}
```

---

## Handy Resource Snippets

Here are some common patterns for exposing data:

### 1. Exposing a Local File System Directory

```typescript
import path from "path";
import fs from "fs/promises";

// Matches any file under the project directory
server.registerResource(
  {
    name: "project-files",
    template: "file:///project/{path}",
    mimeType: "text/plain"
  },
  async (uri, { path: relativePath }) => {
    const validPath = path.resolve("/my/project", relativePath);
    // Security check: ensure we stay within project root
    if (!validPath.startsWith("/my/project")) {
       throw new Error("Access denied");
    }

    const content = await fs.readFile(validPath, "utf-8");
    return {
      contents: [{
        uri: uri.href,
        mimeType: "text/plain",
        text: content
      }]
    };
  }
);
```

### 2. Database Record Access

```typescript
server.registerResource(
  {
    name: "user-profile",
    template: "db://users/{userId}",
    mimeType: "application/json"
  },
  async (uri, { userId }) => {
    const user = await db.query("SELECT * FROM users WHERE id = ?", [userId]);
    
    return {
      contents: [{
        uri: uri.href,
        mimeType: "application/json",
        text: JSON.stringify(user)
      }]
    };
  }
);
```

### 3. Application State / Health

```typescript
server.registerResource(
  {
    name: "app-health",
    uri: "internal://health",
    mimeType: "application/json"
  },
  async (uri) => {
    return {
      contents: [{
        uri: uri.href,
        mimeType: "application/json",
        text: JSON.stringify({
          status: "healthy",
          uptime: process.uptime(),
          timestamp: new Date().toISOString()
        })
      }]
    };
  }
);
```