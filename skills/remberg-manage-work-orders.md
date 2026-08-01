---
name: Create and track work orders
description: Create maintenance work orders in remberg, attach checklists and procedures, and record part stock changes and time.
api: openapi/remberg-work-orders-openapi.json
operations:
  - /v2/work-orders_get
  - /v2/work-orders_post
  - /v2/work-orders/{id}_patch
  - /v2/work-orders/{id}/checklist_post
  - /v2/work-orders/{id}/stock-changes_post
  - /v2/work-orders/{id}/times_get
---

# Create and track work orders

Use the remberg Work Orders API (v2) to drive the maintenance execution loop.

## Auth & conventions
- API key in the `authorization` header; base URL `https://api.remberg.de`.
- Work orders are addressable by internal `id` or by ERP `externalReference` (mirror operations exist under `/v2/work-orders/erp/{externalReference}`). Resolving by external reference returns `409` if several work orders share it.
- Offset pagination via `page`/`limit`.

## Steps
1. List work orders with `/v2/work-orders_get` (filter by `status`, `assignedPersonId`, `dueDateFrom`/`dueDateUntil`, `relatedAssetId`).
2. Create one with `/v2/work-orders_post`; it returns the new id.
3. Update it with `/v2/work-orders/{id}_patch`.
4. Add checklist items with `/v2/work-orders/{id}/checklist_post`.
5. Record consumed parts with `/v2/work-orders/{id}/stock-changes_post` (planned/reserved/taken).
6. Read logged labor with `/v2/work-orders/{id}/times_get`.

## Errors
- `403` — key lacks work-order rights.
- `404` — work order not found.
- `409` — ambiguous external reference (multiple matches).
