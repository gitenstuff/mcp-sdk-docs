# StdioClientTransport Class API

Starts a server as a subprocess and communicates via standard input/output.

## Index

*   [Constructor](#constructor)

## Constructor

```typescript
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";

const transport = new StdioClientTransport({
  command: "python3",
  args: ["server.py"]
});
```

### Parameters
1.  **`config`** (`StdioClientTransportOptions`): Configuration for the subprocess.
    *   `command` (`string`): The executable command to run (e.g., "node", "python3").
    *   `args` (`string[]`, optional): Array of arguments to pass to the command.
    *   `env` (`object`, optional): Environment variables for the subprocess.
    *   `stderr` (`"inherit" | "ignore"`, optional): How to handle the subprocess's stderr output. Defaults to "inherit".
