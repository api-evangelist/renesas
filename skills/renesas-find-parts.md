---
name: Find and parametrically filter Renesas product parts
description: Discover Renesas orderable product parts, narrow them with parametric attribute filters, and retrieve full part detail including pricing, inventory, and documents.
api: openapi/renesas-web-data-openapi-original.yml
operations:
- GET /product-parts
- GET /product-parts/attributes
- POST /product-parts/search
- GET /product-parts/{orderable_id}
---

# Find and filter Renesas product parts

Use the Renesas Web Data API to locate orderable parts and drill into detail.

## Auth
Send `x-api-key: <YOUR_API_KEY>` and `Accept: application/json` on every request.
Base URL: `https://api.renesas.com/web-data/v1`. Keys are issued to approved
partners via the access-request form.

## Steps
1. **Browse parts** — `GET /product-parts?limit=10&offset=0`. Optional filters
   include `part_number`, `part_number_prefix` (e.g. `R5F`), `part_status`, and
   `product_family`. Paginate with `limit` (1–100, default 10) and `offset`.
2. **Discover filterable attributes** — `GET /product-parts/attributes?productId={id}`
   to list the parametric attributes available for a product.
3. **Parametric search** — `POST /product-parts/search` with `productId` in the
   body plus attribute filters using operators `eq`, `gt`, `gte`, `lt`, `lte`,
   `range`. `productId` is required (a 400 `{"error":"productId is required"}` is
   returned otherwise).
4. **Get part detail** — `GET /product-parts/{orderable_id}` for full data:
   `budgetaryPrice` (1/100/1000 breaks), `distiInventory`, `directBuy`,
   `images`, `chipImage`, `cadModel`, `parametricAttributes`, lifecycle.

## Conventions & errors
- Offset pagination; no idempotency keys (all operations are reads).
- Errors are `{ "error": "<message>" }` keyed by HTTP status (400/404/500) — not
  RFC 9457. See errors/renesas-problem-types.yml.
