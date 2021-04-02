---
layout: post
title: Enable trace logs - Java Function App
date: 2021-04-01 22:28 +0530
---

## Enable trace logs - Java Function App

The Java Functions use azure-functions-java-library which uses java.util.logging for logging. You could print the logs using the following code:

### code snippet

```java
context.getLogger().fine("context logtest fine");
context.getLogger().finer("context logtest finer");
context.getLogger().finest("context logtest finest");
context.getLogger().info("context logtest info");
context.getLogger().warning("context logtest warning");
context.getLogger().severe("context logtest severe");
```

You'd notice that the anything above info level logs would not get printed by default. To log these, you would need to change the logging level at the Function Host level. This can be done using the following host.json:

### host.json

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[2.*, 3.0.0)"
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

That's it! :v:
