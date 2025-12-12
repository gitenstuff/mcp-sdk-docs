# TextResourceContents Interface

Represents text content within a resource.

```typescript
interface TextResourceContents {
  uri: string;
  mimeType?: string;
  text: string;
}
```

## Properties

1.  `uri` (`string`): The URI of the resource.
2.  `mimeType` (`string`, optional): The MIME type of the text content.
3.  `text` (`string`): The actual text content.
