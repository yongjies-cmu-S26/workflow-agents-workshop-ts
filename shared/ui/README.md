# @workshop/ui

The **telemetry viewer** shared by all three patterns — a single, mountable Hono
router that renders a table of reviews with drill-in to per-agent findings and
spans. Backed by [`@workshop/db`](../db); no build step, no framework.

## Usage

The host app supplies the write path (`POST /api/reviews`) and mounts the viewer
at the root:

```ts
import { createUiRouter } from '@workshop/ui'

app.route('/', createUiRouter('Pattern 1 — Naive agent'))
```

## What it serves

- `GET /` — the dashboard HTML (a self-contained page; polls the APIs below).
- `GET /api/reviews` — recent reviews.
- `GET /api/reviews/:id` — one review with its findings and spans.

## Key exports

- `createUiRouter(title)` → a `Hono` app to mount.
- `dashboardHtml(title)` → the standalone HTML page (exported for reuse).

## Layout

```
src/
  index.ts   createUiRouter (read APIs) + re-export of dashboardHtml
  page.ts    dashboardHtml — the self-contained viewer page
```
