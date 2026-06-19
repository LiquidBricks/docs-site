---
id: graphql
title: GraphQL API
sidebar_position: 3
---

GraphQL handles mutations by publishing corresponding commands/events to NATS for the Component Manager to process. Queries can read state, but all writes flow through NATS.

- Mutations → NATS: publishes component instance commands for actions such as `create`/`start` and publishes manually stubbed executor results to `*.component-service.*.function_result.evt.component.compute_function.v1.*`.
- Component Manager picks these up, persists/updates graph state, and emits follow-up events.
