---
id: component-manager
title: Component Manager
sidebar_position: 1
---

The Component Manager orchestrates component registration and instance lifecycle.

- Code: `consumer/componentService/`
- Subjects: component and component-instance commands/events under `*.component-service.*.*`, including provided results on `*.component-service.*.function_result.evt.component.compute_function.v1.{type}` and failures on `*.component-service.*.function_result.evt.component.compute_function_failed.v1.{type}`

Responsibilities
- Register component specs: persists component, nodes, and dependency edges from `builder/component.js` descriptors.
- Manage instances: create, start, and accept computed results for specific component instances.
- Route by subject: a small router maps subjects to handlers.

Key handlers
- `component.command: register` — stores the component graph and emits `component.event: registered`.
- `componentInstance.command: create` — creates an instance vertex, initializes state edges for each node, emits `.cmd.create.componentInstance`.
- `componentInstance.command: start` — acknowledges a start request (hook for scheduling/execution).
- `*.component-service.*.function_result.evt.component.compute_function.v1.{type}` — records a provided data, gate, or task result.
- `*.component-service.*.function_result.evt.component.compute_function_failed.v1.{type}` — records a structured computation error without a `result` or `resultValue` field.

Code entrypoints
- Manager: `consumer/componentService/index.js`
- Router: `consumer/componentService/router/index.js`
- Handlers: `consumer/componentService/handlers/...`

Usage examples
Register a component (normally published by the Component Provider):
```js
conn.publish('component.command', JSON.stringify({
  command: 'register',
  data: {/* name, hash, data[], tasks[] */}
}));
```

Create an instance:
```js
conn.publish('componentInstance.command', JSON.stringify({
  command: 'create',
  data: { componentHash: '<hash>', instanceId: 'inst-1' }
}));
```

Provide a computed result to a waiting state (either from the executor or manually):
```js
conn.publish('prod.component-service._.function_result.evt.component.compute_function.v1.data', JSON.stringify({
  data: {
    instanceId: 'inst-1',
    stateId: '<state-vertex-id>',
    name: 'input',
    type: 'data',
    result: { /* any */ },
    status: 'provided',
  }
}));
```
