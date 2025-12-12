# CreateMessageResult Interface

Result returned from the client's LLM after creating a message.

```typescript
interface CreateMessageResult {
  role: "assistant";
  content: {
    type: "text";
    text: string;
  } | {
    type: "image";
    data: string;
    mimeType: string;
  };
  model: string;
  stopReason?: "endTurn" | "stopSequence" | "maxTokens";
}
```

<h2>Properties</h2>

1.  `role` (`"assistant"`): The role of the message sender.
2.  `content` (`object`): The content of the message, which can be text or image.
    1.  `type` (`"text"`): For text content.
    2.  `text` (`string`): The text content.
    3.  `type` (`"image"`): For image content.
    4.  `data` (`string`): Base64 encoded image data.
    5.  `mimeType` (`string`): MIME type of the image.
3.  `model` (`string`): The name of the model that generated the message.
4.  `stopReason` (`"endTurn" | "stopSequence" | "maxTokens"`, optional): The reason the message generation stopped.
