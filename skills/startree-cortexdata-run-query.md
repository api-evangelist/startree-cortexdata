---
name: Run a SQL query on StarTree Cloud
description: Execute a read-only SQL query against a StarTree Cloud (Apache Pinot) real-time analytics table and read the tabular result.
api: openapi/startree-cortexdata-query-openapi.json
operations: [executeQuery]
---

# Run a SQL query on StarTree Cloud

Use this skill to run analytical SQL against a StarTree Cloud broker and parse the result.

## Prerequisites
- A **Bearer token (JWT)**: generate it in the StarTree console under **API Access**.
- For multi-tenant / Free Tier clusters, the **Workspace ID** (e.g. `ws_2kc8e2dnzzb0`) — sent in the `database` header. Dedicated clusters do not need it.
- The broker base URL for your cluster (e.g. `https://broker.pinot.<cluster>.cp.s7e.startree.cloud`).

## Steps
1. Build the request to `executeQuery` — `POST /query/sql` on your broker host.
2. Set headers: `Authorization: Bearer <JWT>`, `Content-Type: application/json`, and (multi-tenant only) `database: <workspaceId>`.
3. Send a JSON body `{ "sql": "SELECT ... FROM <table> LIMIT <n>" }`. Keep queries read-only; use `LIMIT`/`OFFSET` for windowing (there is no wire pagination).
4. Read the `200` response envelope: `resultTable.dataSchema.columnNames` and `columnDataTypes` describe the columns; `resultTable.rows` is the array of row arrays.

## Rules
- The Query API is **not idempotent-keyed** and is read-only — do not expect write semantics here.
- Authentication is required on **every** request; a missing/expired token fails the call.
- Broker hosts are cluster-specific; never hardcode the example host.
