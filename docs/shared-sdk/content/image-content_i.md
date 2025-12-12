# ImageContent Interface

Represents image content used in Tools, Resources, Prompts, and Sampling.

```typescript
interface ImageContent {
  type: "image";
  data: string; // Base64 encoded
  mimeType: string;
}
```

## Properties

1.  `type` (`"image"`): The type of content.
2.  `data` (`string`): Base64 encoded image data.
3.  `mimeType` (`string`): The MIME type of the image (e.g., "image/png").
