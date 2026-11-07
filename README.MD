```markdown
# Divctor

**Simple, fast, and developer-friendly SVG Charts & QR Codes API.**

Divctor turns your data into clean, responsive SVG charts and QR codes with a single HTTP request. No libraries, no setup, no dependencies.

---

## ✨ Features

- **8 Chart Types**: Bar, Line, Pie, Area, Stacked Bar, Donut, Scatter, Radar.
- **Flowcharts**: Generate SVG flowcharts from nodes and links.
- **QR Codes**: Free, unlimited, and independent QR code generator.
- **Save & Share**: Permanent URLs for your charts (`save=true`).
- **Developer-First**: Clean docs, interactive playground, and OpenAPI spec.

---

## 🚀 Quick Start

### 1. Get an API Key
Sign in to the [Dashboard](https://www.divct.workers.dev/dashboard) and create an app.

### 2. Make Your First Request

```bash
curl -o chart.svg "https://api.divct.workers.dev/chart?type=bar&data=10,20,30&apikey=YOUR_KEY"
```

### 3. Embed Anywhere

```html
<img src="https://api.divct.workers.dev/chart?type=bar&data=10,20,30&apikey=YOUR_KEY" alt="Chart">
```

---

## 📚 Documentation

Full documentation is available at **[docs.divct.workers.dev](https://docs.divct.workers.dev)**.

- [API Reference](https://docs.divct.workers.dev/api)
- [Chart Types](https://docs.divct.workers.dev/docs/chart-types)
- [Graph API](https://docs.divct.workers.dev/docs/graph-api)
- [Authentication](https://docs.divct.workers.dev/docs/authentication)
- [Error Codes](https://docs.divct.workers.dev/docs/errors)
- [Interactive API (Swagger)](https://docs.divct.workers.dev/swagger)

---

## 🔧 API Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/chart` | Generate an SVG chart |
| `POST` | `/graph` | Generate an SVG flowchart |

**Base URL:** `https://api.divct.workers.dev`

**Authentication:** Pass your API key as a query parameter (`?apikey=YOUR_KEY`) or via `Authorization: Bearer YOUR_KEY`.

---

## 🆓 Free Tier & Beta

Divctor is currently in **Beta**. All new users get **7,000 free points per day** for **30 days**. After 30 days, you automatically downgrade to **Free** (1,000 points/day). Beta cannot be re-activated.

1 point = 1 data point. A chart with 10 values consumes 10 points. Points reset daily.

---

## 🛠️ Tech Stack

- **Runtime:** Cloudflare Workers
- **Database:** Cloudflare D1
- **API Spec:** OpenAPI 3.0

---

## 📄 License

Copyright (c) 2026 Divctor. All rights reserved.

This repository contains documentation and API specifications only. The source code is proprietary. You may not copy, modify, or distribute any part of this repository or the associated service without prior written permission.

The API itself is provided as a service. You are granted a limited, non-exclusive license to use the API for your applications, subject to the [Terms of Service](https://www.divct.workers.dev/terms).

---

## 📬 Contact

- **Email:** [2ramadanibrahim@gmail.com](mailto:2ramadanibrahim@gmail.com)
- **GitHub Issues:** [Open an issue](https://github.com/Ramadan-ibrahim-eg/Divctor/issues)

---

## 🔗 Links

- **Website:** [www.divct.workers.dev](https://www.divct.workers.dev)
- **Docs:** [docs.divct.workers.dev](https://docs.divct.workers.dev)
- **QR Generator:** [qr.divct.workers.dev](https://qr.divct.workers.dev)
```
