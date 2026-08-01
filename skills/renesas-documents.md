---
name: Retrieve Renesas product documents
description: List and fetch Renesas documents (datasheets, manuals, driver packages) filtered by type, language, and revision date, with lookup by DCP or Agile ID.
api: openapi/renesas-web-data-openapi-original.yml
operations:
- GET /documents/types
- GET /documents
- GET /documents/{dcp_id}
- GET /documents/agile-id/{agile_id}
---

# Retrieve Renesas documents

## Auth
`x-api-key: <YOUR_API_KEY>`, `Accept: application/json`, base URL
`https://api.renesas.com/web-data/v1`.

## Steps
1. **Discover document types** — `GET /documents/types` returns all document
   types and their sub-types (e.g. `Datasheet`, `Manual - Software` /
   `Driver Package`).
2. **List documents** — `GET /documents` with filters: `type`, `subType`,
   `language`, `category`, `documentRevisionDateStart`/`documentRevisionDateEnd`,
   and product filters. Paginate with `limit` (1–100) and `offset`.
3. **Fetch one document** — by DCP Document ID via `GET /documents/{dcp_id}`, or
   by Agile ID via `GET /documents/agile-id/{agile_id}`.

## Notes
- Documents are cross-referenced from product, product-part, board/kit, package,
  and software-tool resources (see data-model/renesas-data-model.yml).
- Offset pagination; `{ "error": "<message>" }` envelope on 400/404/500.
