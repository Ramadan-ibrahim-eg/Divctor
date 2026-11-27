![Logo](logo.png)
# Divctor

Charts, flowcharts and QR codes as SVG from a single HTTP request. No SDK, no client-side JavaScript, no build step.

Runs on Cloudflare Workers. The output is plain SVG, which you can use in `<img>` tags, static sites, PDFs and documentation. Check SVG support in your own target.

| Service | URL |
| --- | --- |
| Website and dashboard | https://www.divct.workers.dev |
| API | https://api.divct.workers.dev |
| Documentation | https://docs.divct.workers.dev |
| Interactive API reference | https://docs.divct.workers.dev/swagger |
| OpenAPI spec | https://docs.divct.workers.dev/openapi.yaml |
| QR generator | https://qr.divct.workers.dev |
| MCP endpoint (for AI assistants) | https://divctor-mcp.divct.workers.dev/mcp |

## Quick start

1. Sign in on the [website](https://www.divct.workers.dev) with Google or GitHub.
2. In the dashboard, open "Apps & API Keys", click "+ New App" and copy your API key.
3. Send a request:

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
  "https://api.divct.workers.dev/chart?type=bar&data=30,50,40&labels=A,B,C" -o chart.svg
```

You can also pass the key as `?apikey=YOUR_API_KEY`, but URLs with keys can end up in logs. Prefer the `Authorization` header.

A chart with several groups and a legend:

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
  "https://api.divct.workers.dev/chart?type=bar&data=45,52,38,65&data2=30,40,35,50&names=Sales,Costs&labels=Q1,Q2,Q3,Q4" -o grouped.svg
```

Embed a result in HTML:

```html
<img src="https://api.divct.workers.dev/chart?type=pie&data=35,25,20,20&labels=Q1,Q2,Q3,Q4&apikey=YOUR_API_KEY" alt="Pie chart">
```

An API key in a public page is visible to anyone. Restrict the key to your domains in the dashboard, or use `save=true` (below) and embed the clean saved URL instead.

## Endpoints

| Method | Path | Description | Auth |
| --- | --- | --- | --- |
| GET | `/chart` | Generate an SVG chart from URL parameters | API key |
| POST | `/chart` | The same chart from a JSON body | API key |
| POST | `/graph` | Generate an SVG flowchart from JSON | API key |
| GET | `/saved/{id}` | Fetch a saved chart or flowchart | None |
| DELETE | `/saved/{id}` | Delete a saved item you own | API key |
| GET | `/health` | Health check (JSON) | None |
| GET | `/` | API info (JSON) | None |

### Charts

`/chart` supports 8 types: `bar`, `line`, `pie`, `area`, `stacked-bar`, `donut`, `scatter`, `radar`. Default is `bar`. GET and POST give the same result, with the same validation, points and errors. Use POST when the data is too long for a URL (JSON body up to 200,000 characters, `Content-Type: application/json`; send the key in the header or the `apikey` query parameter, not in the body).

```bash
curl -X POST "https://api.divct.workers.dev/chart" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"type":"line","data":[10,25,35,50],"labels":["Jan","Feb","Mar","Apr"],"title":"Growth"}' -o line.svg
```

Main parameters (only `data` is required):

| Parameter | Description |
| --- | --- |
| `type` | Chart type |
| `data` | Comma-separated values, max 100 per dataset. For `scatter`, a JSON array of `{"x":..,"y":..}` objects |
| `data2` to `data5` | Extra groups for bar, stacked-bar, line, area, scatter and radar. Not allowed for pie and donut. Bar and stacked-bar groups need the same number of values |
| `labels` | Comma-separated labels. They name the slices for pie and donut. Radar needs at least 3 labels and one value per label in every group |
| `names` | Group names for the legend, in the order `data`, `data2` to `data5` (up to 60 characters each, no commas). Not allowed for pie and donut |
| `legend` | Omit for automatic (shown with 2 or more groups). `true` always shows it, `false` hides it. The legend adds about 38 px of height and costs no points |
| `title` | Chart title |
| `showValues` | Show or hide values (default `true`) |
| `pointStyle`, `pointEmoji`, `pointSize` | Point appearance for line, scatter and radar |
| `lineWidth`, `borderRadius`, `bgStyle`, `customCSS` | Styling. `customCSS` is sanitized |
| `save`, `expiryDays` | Save the result, see below |

Full parameter details and defaults are in the [OpenAPI spec](https://docs.divct.workers.dev/openapi.yaml).

### Flowcharts

```bash
curl -X POST "https://api.divct.workers.dev/graph" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "nodes": [
      {"id": "a", "label": "Start"},
      {"id": "b", "label": "Build"},
      {"id": "c", "label": "Deploy"}
    ],
    "links": [
      {"source": "a", "target": "b"},
      {"source": "b", "target": "c"}
    ]
  }' -o flow.svg
```

Up to 50 nodes and 100 links. Each node needs an `id`; `label` is optional and defaults to the id. Link `source` and `target` must match a node `id`, otherwise the request fails with 400. Optional `options` control colors, node size, gaps and layering.

### Saving a result

Add `save=true` to `/chart` or `/graph` to get JSON (HTTP 200) instead of SVG:

```json
{ "saved": true, "url": "...", "expires_at": "...", "expires_in_days": 30 }
```

The returned URL opens the saved SVG without a key or generation parameters. It is valid until `expires_at`: 30 days by default, or 60 with `expiryDays=60`. Other values are treated as 30. Expired items return 404. Delete a saved item you own with `DELETE /saved/{id}` and your API key.

### QR codes

The QR service needs no API key and is free and unlimited. It returns SVG.

```bash
curl "https://qr.divct.workers.dev/qr?text=Hello&size=400&ecc=M&style=classic" -o qr.svg
```

| Endpoint | Description |
| --- | --- |
| `GET /qr` | QR code as SVG |
| `GET /qr-info` | Metadata (version, modules, density, ecc) |

Parameters: `text` (required, max 1000 characters), `size` (100 to 1000, default 400), `ecc` (`L`, `M`, `Q`, `H`; default `M`), `fg`, `bg` (hex), `style` (`classic`, `rounded`, `dots`, `smooth`, `modern`), `border` (0 to 10), `borderRadius` (0 to 100).


## MCP server (for AI assistants)

Divctor has an MCP (Model Context Protocol) endpoint. AI assistants that support MCP can draw charts, flowcharts and QR codes through it, without an API key.

```
https://divctor-mcp.divct.workers.dev/mcp
```

Streamable HTTP transport. Example for Claude Code:

```bash
claude mcp add --transport http divctor https://divctor-mcp.divct.workers.dev/mcp
```

Tools: `generate_chart` (all 8 chart types; saves and returns a shareable link by default, `save=false` returns the raw SVG), `generate_graph` (flowcharts, same save behavior), `generate_qr` (QR as SVG, free), `list_chart_types` (the types and their data rules, free).

Anonymous use is free: no points are deducted from any account, and generated charts carry the Divctor watermark (like the Free plan). Each client IP can send up to 30 requests per minute; over the limit, calls return 429. To draw on your own account instead, pass your API key in the optional `api_key` argument of `generate_chart` or `generate_graph`; the call then follows your plan's points and rules, exactly like a direct API call.

## Authentication

Pass the key as `?apikey=YOUR_API_KEY` or as `Authorization: Bearer YOUR_API_KEY`. If you send both with different values, the request fails with 400.

Optional protections, configured per key: domain restrictions, and HMAC-SHA256 request signing via the `X-Signature` header. Never put a key in client-side code you do not restrict by domain. See the [authentication docs](https://docs.divct.workers.dev/docs/authentication) and [security guide](https://docs.divct.workers.dev/docs/security).

## Errors

Errors return JSON:

```json
{
  "error": "Invalid API key",
  "code": "UNAUTHORIZED",
  "docs": "https://docs.divct.workers.dev",
  "dashboard": "https://www.divct.workers.dev"
}
```

Codes: `INVALID_DATA`, `INVALID_TYPE`, `UNAUTHORIZED`, `DOMAIN_NOT_ALLOWED`, `INVALID_SIGNATURE`, `FORBIDDEN`, `ACCOUNT_SUSPENDED`, `NOT_FOUND`, `RATE_LIMITED`, `QUOTA_EXCEEDED`, `INTERNAL_ERROR`, `SERVICE_UNAVAILABLE`. A request that fails validation or is rejected for quota is not charged (see billing below). Details are on the [error codes page](https://docs.divct.workers.dev/docs/errors).

## Plans and limits

| Plan | Points |
| --- | --- |
| Beta | 7,000 per day, renews at 00:00 Cairo time |
| Free | 2,500 per month, renews on the 1st at 00:00 Cairo time |

- A Beta account that uses its daily points continues on the Free monthly points. When both are used, requests fail with `429 QUOTA_EXCEEDED` and a `Retry-After` header.
- 1 point = 1 data point, counted across all datasets of the request, so a chart with 10 values costs 10 points (minimum 1). A flowchart costs 1 point per node. QR codes cost nothing. Unused points do not roll over.
- Billing is success-only: points are charged only after the chart or flowchart is fully built, in a single database transaction together with the saved item. A request that fails before that (validation error, bad key, rate limit, quota, generation error) is not charged. In the rare case where a response is lost after the charge was committed, the points are still counted.
- Each API key can send up to 60 requests per 60 seconds. The limit is approximate and applies separately in each Cloudflare location. Above it you get `429 RATE_LIMITED`.
- Maximum 100 values per dataset, up to 5 datasets per chart.

Current numbers are always on the [rate limits page](https://docs.divct.workers.dev/docs/rate-limits).

## Documentation

- [Guides](https://docs.divct.workers.dev)
- [Interactive API reference](https://docs.divct.workers.dev/swagger)
- [OpenAPI 3 spec](https://docs.divct.workers.dev/openapi.yaml)

## Support

See the [contact page](https://www.divct.workers.dev/contact).
