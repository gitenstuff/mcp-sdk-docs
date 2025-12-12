# Resource Interface

Represents a resource available on the server.

```typescript
interface Resource {
  uri: string;
  name: string;
  description?: string;
  mimeType?: string;
}
```

<h2>Properties</h2>

1.  `uri` (`string`): The unique URI of the resource.
2.  `name` (`string`): A human-readable name for the resource.
3.  `description` (`string`, optional): A description of the resource.
4.  `mimeType` (`string`, optional): The MIME type of the resource content.
