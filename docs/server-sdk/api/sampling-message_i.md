# SamplingMessage Interface

Represents a message used in sampling requests to the client's LLM.

```typescript
interface SamplingMessage {
  role: "user" | "assistant";
  content: {
      type: "text";
      text: string;
  } | {
      type: "image";
      data: string;
      mimeType: string;
  };
}
```

<h2>Properties</h2>

1.  `role` (`"user" | "assistant"`): The role of the message sender.
2.  `content` (`object`): The content of the message, which can be text or image.
    1.  `type` (`"text"`): For text content.
    2.  `text` (`string`): The text content.
    3.  `type` (`"image"`): For image content.
    4.  `data` (`string`): Base64 encoded image data.
    5.  `mimeType` (`string`): MIME type of the image.
