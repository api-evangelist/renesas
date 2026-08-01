---
name: Cross-reference competitor parts to Renesas replacements
description: Submit up to 200 competitor part numbers and get matching Renesas replacement parts with match type and lifecycle status.
api: openapi/renesas-web-data-openapi-original.yml
operations:
- POST /product-parts/cross-reference
- GET /product-parts/{orderable_id}
---

# Cross-reference competitor parts

Find Renesas replacements for competitor part numbers in bulk.

## Auth
`x-api-key: <YOUR_API_KEY>`, `Accept: application/json`, base URL
`https://api.renesas.com/web-data/v1`.

## Steps
1. **Submit the batch** — `POST /product-parts/cross-reference` with up to **200**
   competitor part numbers per request. Inputs with no match are returned with an
   empty results array (not an error), so always iterate the full response.
2. **Read each match** — every result carries the match `type` (`Drop-in`,
   `Footprint`, or `Functional`), competitor manufacturer, lifecycle status, part
   description, and comments.
3. **Pull replacement detail** — for promising matches, call
   `GET /product-parts/{orderable_id}` to get pricing, inventory, documents, and
   parametric attributes of the Renesas replacement.

## Notes
- This POST is a read-only query (no state change, no idempotency key needed).
- A 400 indicates a malformed body; 500 is a server error — retry with backoff.
