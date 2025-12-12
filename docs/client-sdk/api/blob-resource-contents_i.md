# BlobResourceContents Interface

Represents binary blob content within a resource.

```typescript
interface BlobResourceContents {
  uri: string;
  mimeType?: string;
  blob: string; // Base64 encoded data
}
```

## Properties

1.  `uri` (`string`): The URI of the resource.
2.  `mimeType` (`string`, optional): The MIME type of the binary content.
3.  `blob` (`string`): Base64 encoded binary data.
