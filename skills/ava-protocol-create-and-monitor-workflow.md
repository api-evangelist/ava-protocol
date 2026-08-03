---
name: Create and monitor an onchain automation workflow
description: Simulate, create, and watch an Ava Protocol workflow through to execution.
api: openapi/ava-protocol-avs-openapi-original.yml
operations: [simulateWorkflow, estimateWorkflowFees, createWorkflow, getWorkflow, listExecutionsForWorkflow, getExecution, streamExecution]
---

# Create and monitor a workflow

A workflow connects a **trigger** (manual / fixedTime / cron / block / event) to one or more
**nodes** (ethTransfer, contractWrite, contractRead, restApi, customCode, branch, filter, loop, …).
Authenticate first (see ava-protocol-authenticate.md).

## Steps

1. **Dry-run first** — call **simulateWorkflow** (`POST /workflows:simulate`) with the workflow
   definition to validate it without persisting.
2. **Preview cost** — call **estimateWorkflowFees** (`POST /workflows:estimateFees`) to see the
   per-execution fee.
3. **Create** — call **createWorkflow** (`POST /workflows`) with `{ smartWalletAddress, trigger, nodes, edges }`.
   Note: create is **not** idempotent (no Idempotency-Key), so do not blindly retry a create on timeout —
   list first to check whether it landed.
4. **Confirm** — call **getWorkflow** (`GET /workflows/{id}`) and check `status`.
5. **Watch executions** — poll **listExecutionsForWorkflow** (`GET /workflows/{id}/executions`) or
   subscribe with **streamExecution** (`GET /executions/{id}:stream`, Server-Sent Events); fetch full
   detail with **getExecution** (`GET /executions/{id}`).

## Rules

- Pagination is cursor-based: `{ data, pageInfo }`; pass `pageInfo.endCursor` to `after`.
- Handle `429` by honoring `Retry-After` / `X-RateLimit-Reset`.
- All ids are ULIDs; all addresses are EIP-55 checksummed.
