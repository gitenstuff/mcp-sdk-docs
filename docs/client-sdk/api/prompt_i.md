# Prompt Interface

Represents a prompt template available on the server.

```typescript
interface Prompt {
  name: string;
  description?: string;
  arguments?: {
    name: string;
    description?: string;
    required?: boolean;
  }[];
}
```

<h2>Properties</h2>

1.  `name` (`string`): The name of the prompt.
2.  `description` (`string`, optional): A description of the prompt.
3.  `arguments` (`object[]`, optional): An array of arguments the prompt accepts.
    1.  `name` (`string`): The name of the argument.
    2.  `description` (`string`, optional): A description of the argument.
    3.  `required` (`boolean`, optional): Indicates if the argument is required.
