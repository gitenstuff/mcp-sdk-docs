# StdioServerTransport Class API

The `StdioServerTransport` facilitates communication over standard input/output (stdin/stdout). This transport is ideal for command-line interface (CLI) applications and scenarios where the server and client are co-located within the same process or communicate via piped streams.

## Index

*   [Constructor](#constructor)

## Constructor

```typescript
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';
import process from 'node:process';

const transport = new StdioServerTransport(process.stdin, process.stdout);
```

### Parameters
1.  **`stdin`** (`Readable`, optional): The readable stream to use as input. Defaults to `process.stdin`.
2.  **`stdout`** (`Writable`, optional): The writable stream to use as output. Defaults to `process.stdout`.
