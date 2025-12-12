# ReadResourceResult Interface

Represents the result of reading a resource.

```typescript
interface ReadResourceResult {
  contents: (TextResourceContents | BlobResourceContents)[];
}
```

## Properties

1.  `contents` (`(TextResourceContents | BlobResourceContents)[]`): An array containing the content of the resource.
