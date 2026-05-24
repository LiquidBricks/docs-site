---
id: component-manager-consumer
title: Component Manager
sidebar_position: 1
---

Overview
- Consumes component-service stream messages and routes commands/events for components and instances.

Configuration
- Stream: `COMPONENT_SERVICE_STREAM`
- Durable name: `componentServiceConsumer`
- Ack policy: Explicit
- Deliver policy: All
- Filters:
  - `*.component-service.*.*.cmd.>`
  - `*.component-service.*.*.evt.>`

Code
- Entry: `consumer/componentService/index.js`
- Router: `consumer/componentService/router/index.js`
- Handlers: `consumer/componentService/handlers/...`

Usage
```js
import { componentServiceConsumer } from 'consumer/componentService/index.js'

await componentServiceConsumer({
  streamName: 'COMPONENT_SERVICE_STREAM',
  natsContext,
  g,                // graph context
  diagnostics,
})
```

