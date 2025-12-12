# StreamableHTTPServerTransportOptions Interface

Options for configuring `StreamableHTTPServerTransport`.

```typescript
interface StreamableHTTPServerTransportOptions {
  sessionIdGenerator?: () => string | undefined;
  enableJsonResponse?: boolean;
  enableDnsRebindingProtection?: boolean;
  allowedHosts?: string[];
  allowedOrigins?: string[];
  onsessioninitialized?: (sessionId: string) => void;
  onsessionclosed?: (sessionId: string) => void;
}
```

<h2>Properties</h2>

1.  `sessionIdGenerator` (`() => string | undefined`, optional): A function to generate custom session IDs.
2.  `enableJsonResponse` (`boolean`, optional): Enables returning JSON responses for certain requests.
3.  `enableDnsRebindingProtection` (`boolean`, optional): Enables protection against DNS rebinding attacks.
4.  `allowedHosts` (`string[]`, optional): A list of allowed hostnames for requests.
5.  `allowedOrigins` (`string[]`, optional): A list of allowed origins for CORS.
6.  `onsessioninitialized` (`(sessionId: string) => void`, optional): Callback function when a new session is initialized.
7.  `onsessionclosed` (`(sessionId: string) => void`, optional): Callback function when a session is closed.
