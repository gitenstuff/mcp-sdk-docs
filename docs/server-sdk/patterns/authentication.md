# Authentication Patterns

Implementing robust authentication is crucial for securing your MCP server. The SDK provides utilities and middleware to integrate with OAuth 2.0 based authentication flows, allowing you to protect your resources, tools, and prompts.

## `AuthInfo` Interface

When a client makes an authenticated request, information about the validated access token is attached to the request object, accessible via the `AuthInfo` interface. This interface provides details such as the client ID, scopes, and token expiration.

```typescript
export interface AuthInfo {
    token: string;
    clientId: string;
    scopes: string[];
    expiresAt?: number; // In seconds since epoch
    resource?: URL;
    extra?: Record<string, unknown>;
}
```

---

## Bearer Token Authentication

The `requireBearerAuth` middleware is an Express.js-compatible middleware that enforces the presence and validity of a Bearer token in the `Authorization` header.

### When to Use

*   Protecting HTTP endpoints that serve MCP requests (e.g., POST, GET for `StreamableHTTPServerTransport`).
*   Ensuring only authorized clients with valid tokens and sufficient scopes can access server capabilities.

### Usage

Integrate `requireBearerAuth` into your Express.js or compatible HTTP server setup. It requires an `OAuthTokenVerifier` to validate the incoming tokens.

```typescript
import express from 'express';
import { requireBearerAuth } from '@modelcontextprotocol/sdk/server/auth/middleware/bearerAuth.js';
import { StreamableHTTPServerTransport } from '@modelcontextprotocol/sdk/server/streamableHttp.js';
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';
import { DemoInMemoryAuthProvider } from '@modelcontextprotocol/sdk/server/auth/provider.js'; // Example verifier

const app = express();
const authProvider = new DemoInMemoryAuthProvider(); // Replace with your actual OAuth provider

const httpTransport = new StreamableHTTPServerTransport({
  // ... other options ...
});

const mcpServer = new McpServer({ name: 'auth-server', version: '1.0.0' });
mcpServer.connect(httpTransport);

app.use(express.json()); // For parsing application/json

// Protect all MCP endpoints with Bearer authentication
app.post('/mcp', 
  requireBearerAuth({ 
    verifier: authProvider,
    requiredScopes: ['mcp.read', 'mcp.write'],
    resourceMetadataUrl: 'https://example.com/oauth/mcp-resource-metadata' // Optional
  }),
  async (req, res) => {
    // The validated auth info is available at req.auth
    console.log('Client ID:', req.auth?.clientId);
    console.log('Scopes:', req.auth?.scopes);

    await httpTransport.handleRequest(req, res, req.body);
  }
);

app.get('/mcp', /* ... same middleware and handling for GET requests ... */);

app.listen(3000, () => console.log('Auth-protected server running on port 3000'));
```

### `requireBearerAuth` Options

*   **`verifier`** (`OAuthTokenVerifier`): An object responsible for verifying the authenticity and validity of the OAuth access token.
*   **`requiredScopes`** (`string[]`, optional): An array of strings specifying the OAuth scopes that the token *must* possess for the request to be authorized. If the token lacks any of these scopes, a `403 Forbidden` response (`InsufficientScopeError`) is returned.
*   **`resourceMetadataUrl`** (`string`, optional): A URL pointing to the OAuth 2.0 Protected Resource Metadata. If provided, this URL is included in the `WWW-Authenticate` header for `401 Unauthorized` responses, aiding clients in discovering authentication configuration.

### Error Handling

The middleware automatically handles common OAuth-related errors by sending appropriate HTTP status codes and `WWW-Authenticate` headers:

*   **`401 Unauthorized`**: For missing or invalid tokens (`InvalidTokenError`).
*   **`403 Forbidden`**: For tokens with insufficient scopes (`InsufficientScopeError`).
*   **`400 Bad Request`**: For malformed OAuth requests (`OAuthError`).
*   **`500 Internal Server Error`**: For unexpected server-side issues (`ServerError`).

---

## Custom Authentication Providers

You can provide your own implementation of `OAuthTokenVerifier` or `OAuthServerProvider` to integrate with existing identity management systems or custom token validation logic.