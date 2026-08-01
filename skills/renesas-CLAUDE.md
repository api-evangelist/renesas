# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Redocly Realm** documentation site for the Renesas Web Data API — a static site that renders OpenAPI specifications and markdown guides into interactive developer documentation.

## Development Commands

```bash
npm start    # Start local preview server (runs npx @redocly/cli preview)
```

There are no tests, build steps, or lint scripts — this is a documentation-only repository.

## Architecture

The site is built on [Redocly Realm](https://redocly.com/realm) (Next.js-based). The main content types are:

### 1. OpenAPI Specification (`apis/web-data/`)

- `index.yaml` — root spec file; references all paths and components
- `paths/` — one YAML file per resource (`products.yaml`, `product_parts.yaml`, `documents.yaml`, `boards_kits.yaml`, `packages.yaml`, `software_tools.yaml`)
- `components/schemas/` — shared data models referenced across path files


All endpoints use `$ref` to link schemas and path definitions together. When adding or modifying an endpoint, update the path file and ensure any new schemas are added to `components/schemas/` and referenced from `index.yaml`.

**Known spec inconsistencies:** In `packages.yaml`, the path parameter is named `package_id` but the URL template in `index.yaml` uses `{package_code}`. Similarly, `software_tools.yaml` uses `tool_id` but the URL template uses `{software_tool_id}`. The data model fields are `packageCode` and `softwareToolName` respectively.

### 2. Developer Guides (`guides/`)

- Markdown files rendered as prose documentation
- `sidebars.yaml` controls guide navigation structure
- Guides use Markdoc syntax; custom tags (`Split`) are defined in `@theme/markdoc/`


### 3. Theme & Custom Components (`@theme/`)

- `styles.css` — Renesas brand colors and CSS overrides
- `components/Navbar/` — custom navbar component
- `components/CardWithCode/` — code example card used on the home page
- `markdoc/` — custom Markdoc tag definitions (schema + React components)
- `fonts/` — DM Sans font files (Regular, Medium, SemiBold, Bold in .ttf and .woff2)
- `Templates/StepByStep.tsx` — step-by-step guide template component


### 4. Home Page

- `index.page.tsx` — React/TSX page at the site root; uses styled-components


### 5. Respect Monitoring (`arazzo/`)

Arazzo workflow files for Redocly Respect Monitoring, configured in `redocly.yaml` under `reunite.jobs`.

- `health-check.arazzo.yaml` — runs every **12h**; hits all 6 list endpoints with `limit=1` and checks for `200`. Includes minimum record count assertions:
  - Product Parts: `>= 85,000`
  - Documents: `>= 125,000`
  - Boards & Kits: `> 2,000`
- `deployment-validation.arazzo.yaml` — runs every **7d**; chains list → get-by-ID for all 6 resources, plus exercises `/product-parts/search` (POST) and `/product-parts/attributes`. Severity is set to `error` for all checks (status code, schema, content type, success criteria) so any schema mismatch against the OpenAPI spec fails the job.


Both jobs receive `apiKey` via the `RENESAS_API_KEY` environment variable (set in Reunite project settings). The free tier allows 1,000 requests/month; current setup uses well under that (~60 health check + ~10 validation executions/month).

## Key Configuration

- `redocly.yaml` — controls navbar, footer, theme settings, Respect Monitoring jobs, and which API spec is mounted. The `reunite:` key must be uncommented for jobs to work.
- `apis/web-data/index.yaml` — OpenAPI 3.0.0 root with `servers`, `security`, and `$ref` path includes


## API Details

- Base URL: `https://api.renesas.com/web-data/v1`
- Staging URL: `https://api.renesas-dev.com/web-data/v1`
- Auth: `x-api-key` header
- Resources: Products, Product Parts, Documents, Boards & Kits, Packages, Software Tools
- Product Parts has `/search` (POST, requires `productId` in body) and `/attributes` (GET, optional `productId` filter) sub-endpoints for parametric filtering
- Pagination: `limit` (1–100, default 10) and `offset` parameters on list endpoints


## Directory Structure

```
├── @theme/                  # Theme overrides, components, fonts, Markdoc tags
├── apis/
│   ├── web-data/            # Main API spec (OpenAPI 3.0.0)
│   │   ├── index.yaml       # Root spec file
│   │   ├── paths/           # One YAML per resource
│   │   └── components/schemas/  # Shared data models
│   └── internal-data/       # Internal Data API spec (secondary)
├── arazzo/                  # Respect Monitoring Arazzo workflows
├── guides/                  # Markdoc developer guides
├── images/                  # Logo, favicon, background assets
├── legal/                   # Legal pages (responsible use)
├── tools/                   # CLI, SDK, replay tool docs
├── index.page.tsx           # Home page
├── changelog.md             # Changelog
├── redocly.yaml             # Site-wide config
└── package.json
```