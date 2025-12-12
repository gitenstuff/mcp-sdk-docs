# EmbeddedResource Interface

Represents an embedded resource used in Tools, Resources, Prompts, and Sampling.

```typescript
interface EmbeddedResource {
  type: "resource";
  resource: {
    uri: string;
    mimeType?: string;
    text?: string;
    blob?: string; // Base64 encoded
  };
}
```

<h2>Properties</h2>

1.  `type` (`"resource"`): The type of content.
2.  `resource` (`object`): Details about the embedded resource.
    1.  `uri` (`string`): The URI of the resource.
    2.  `mimeType` (`string`, optional): The MIME type of the resource content.
    3.  `text` (`string`, optional): Text content of the resource (if applicable).
    4.  `blob` (`string`, optional): Base64 encoded binary data of the resource (if applicable).
