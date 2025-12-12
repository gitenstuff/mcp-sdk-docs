# PromptMessage Interface

Represents a single message within a prompt, typically for an LLM.

```typescript
interface PromptMessage {
  role: "user" | "assistant";
  content: {
    type: "text";
    text: string;
  } | {
    type: "image";
    data: string;
    mimeType: string;
  } | {
    type: "resource";
    resource: {
        uri: string;
        text?: string;
        blob?: string;
        mimeType?: string;
    };
  };
}
```

## Properties

1.  `role` (`"user" | "assistant"`): The role of the message sender.
2.  `content` (`object`): The content of the message, which can be text, image, or a resource.
    1.  `type` (`"text"`): For text content.
    2.  `text` (`string`): The text content.
    3.  `type` (`"image"`): For image content.
    4.  `data` (`string`): Base64 encoded image data.
    5.  `mimeType` (`string`): MIME type of the image.
    6.  `type` (`"resource"`): For resource content.
    7.  `resource` (`object`): Details about the resource.
        1.  `uri` (`string`): URI of the resource.
        2.  `text` (`string`, optional): Text content of the resource.
        3.  `blob` (`string`, optional): Base64 encoded binary data of the resource.
        4.  `mimeType` (`string`, optional): MIME type of the resource.
