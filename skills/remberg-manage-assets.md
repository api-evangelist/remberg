---
name: Manage assets and status signals
description: Create and update equipment assets in remberg and record/resolve their operational status signals.
api: openapi/remberg-assets-openapi.json
operations:
  - /v2/assets_get
  - /v2/assets_post
  - /v2/assets/{id}_patch
  - /v2/assets/{id}/status-signals_post
  - /v2/assets/{id}/status-signals/resolve_patch
---

# Manage assets and status signals

Use the remberg Assets API (v2) to keep the equipment fleet in sync and to record operational state.

## Auth & conventions
- Send the API key in the `authorization` header (apiKey, header). Keys are created under Settings > Data > API and inherit the signing user's rights.
- Base URL: `https://api.remberg.de`.
- Keep under ~5 req/s (burst 10/1s, base 25/5s); on `429`, honor `Retry-After-Base`/`Retry-After-Burst`.
- No idempotency-key header exists — guard creates yourself. `POST /v2/assets` returns `409` if an asset with the same number already exists.

## Steps
1. List existing assets with `/v2/assets_get` (paginate with `page`/`limit`; filter with `search`, `assetTypeLabel`, `assetStatus`).
2. Create a new asset with `/v2/assets_post`; capture the returned id. A `409` means the asset number is taken — fetch it instead.
3. Update fields with `/v2/assets/{id}_patch` (returns `204`).
4. Record operational state with `/v2/assets/{id}/status-signals_post` (OK / warning / error, optional comment).
5. Clear alerts with `/v2/assets/{id}/status-signals/resolve_patch` once addressed.

## Errors
- `403` — API key lacks access rights for assets.
- `404` — asset id not found in this tenant.
- `409` — duplicate asset number, or the asset is a parent to other assets (blocks delete).
