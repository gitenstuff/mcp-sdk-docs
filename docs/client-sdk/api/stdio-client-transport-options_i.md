# StdioClientTransportOptions Interface

Options for configuring `StdioClientTransport`.

```typescript
interface StdioClientTransportOptions {
  command: string;           // Executable path
  args?: string[];           // Command line arguments
  env?: NodeJS.ProcessEnv;   // Environment variables
  stderr?: "inherit" | "ignore";
}
```

## Properties

1.  `command` (`string`): The executable path to run as a subprocess.
2.  `args` (`string[]`, optional): Command line arguments for the subprocess.
3.  `env` (`NodeJS.ProcessEnv`, optional): Environment variables for the subprocess.
4.  `stderr` (`"inherit" | "ignore"`, optional): Specifies how to handle the subprocess's stderr.
