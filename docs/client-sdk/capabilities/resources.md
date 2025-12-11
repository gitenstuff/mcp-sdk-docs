# Consuming Resources

Clients can read data resources exposed by the server.

## Listing Resources

```typescript
const result = await client.listResources();
```

## Reading a Resource

```typescript
const result = await client.readResource({
  uri: "file:///config.json"
});

const content = result.contents[0];
if ("text" in content) {
  console.log(content.text);
}
```

## Subscribing (Optional)

If the server supports it, you can subscribe to updates.

```typescript
await client.subscribeResource({
  uri: "file:///logs/app.log"
});

// Listen for notifications
client.on("notification", (notification) => {
  if (notification.method === "notifications/resources/updated") {
    console.log("Resource changed:", notification.params.uri);
  }
});
```
