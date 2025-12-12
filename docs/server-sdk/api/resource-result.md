# ResourceResult Interface

Return type for resource handlers.

```typescript
interface ResourceResult {
  contents: (TextResourceContents | BlobResourceContents)[];
}
```

## Properties

1.  `contents` (`(TextResourceContents | BlobResourceContents)[]`): An array containing the content of the resource.
