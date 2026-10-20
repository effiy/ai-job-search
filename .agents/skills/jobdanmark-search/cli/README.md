---
title: jobdanmark-cli
tags: [cli, jobdanmark, job-search, denmark, bun]
category: 工具
created: 「待补充」
updated: 「待补充」
source: https://jobdanmark.dk
type: CLI工具
status: 活跃
lifecycle: 稳定
---

# jobdanmark-cli

CLI for the [Jobdanmark.dk](https://www.jobdanmark.dk) public job search API.

**Base URL**: `https://jobdanmark.dk`
**Authentication**: None required.
**Format**: All responses are JSON.

---

## 💡 概述

A Bun-based CLI tool for searching and retrieving job listings from Jobdanmark.dk, a Danish job portal. Provides search, detail, category listing, autocomplete, and location suggestion commands.

---

## ⚡ 功能

### Commands

| Command | Description |
|---------|-------------|
| `search` | Search job listings with filters |
| `detail` | Full detail for a single job posting (by slug) |
| `categories` | List all job categories with live counts |
| `autocomplete` | Suggest job titles and categories for a query |
| `locations` | Suggest municipalities, zip codes, and regions for a query |

All commands accept `--format json|table|plain` (default: `json`).
All errors are written to **stderr** as `{ "error": "...", "code": "..." }` and the process exits with code `1`.

### `search` — Search job listings

**Endpoint**: `POST https://jobdanmark.dk/api/jobsearch/search/{page}`

```bash
bun run src/cli.ts search [flags]
```

#### Flags

| Flag | Type | Default | Description |
|------|------|---------|-------------|
| `--text` | string | — | Free-text keyword search (job title, keyword) |
| `--category` | number | — | Category ID (see table below) |
| `--jobtitle-id` | number | — | Job title ID from `autocomplete` results |
| `--municipality` | string | — | Municipality name, e.g. `Odense`, `København` |
| `--zip` | string | — | Zip code, e.g. `5000` |
| `--region` | string | — | Landsdel: a `locations` item with category `region`, e.g. `Sjælland`, `Østjylland` |
| `--administrative-region` | string | — | Administrative region: a `locations` item with category `administrativeRegion`, e.g. `Region Midtjylland` |
| `--job-type` | string | — | Comma-separated job types: `fuldtid,deltid,fleksjob,elev,studiejob,praktik` |
| `--page` | number | `1` | Page number (30 items per page, server-enforced) |
| `--limit` | number | — | Cap total results returned by CLI (client-side) |
| `--format` | string | `json` | Output format: `json`, `table`, `plain` |

> **Note**: Per-page is fixed at 30 by the API. There is no `--per-page` flag.
> **Note**: Multiple `--job-type` values are passed as a comma-separated string and split by the CLI into the `jobTypes` array in the request body.

#### Example

```bash
bun run src/cli.ts search --text "elektriker" --job-type fuldtid --municipality "Odense"

bun run src/cli.ts search --category 227978 --job-type "fuldtid,deltid" --page 2 --format table

bun run src/cli.ts search --text "sygeplejerske" --zip 8000 --limit 10
```

#### Request body sent to API

```json
{
  "jobTypes": ["fuldtid"],
  "filters": [
    { "type": "freetext", "value": "elektriker", "displayText": "elektriker" },
    { "type": "municipality", "value": "Odense", "displayText": "Odense" }
  ],
  "locationMode": "Text",
  "distance": 50
}
```

> **Important**: All filter objects include a `displayText` field (required by the server). The `value` field is an integer for `category` and `jobtitle` filter types; a string for all others.

#### Response shape

```json
{
  "meta": {
    "currentPage": 1,
    "totalItems": 15610,
    "itemsPrPage": 30,
    "totalPages": 521
  },
  "results": [
    {
      "title": "IT-chef søges til RAH",
      "companyName": "Rah Service A/S",
      "companyAddress": "Ndr Ringvej 4 6950 Ringkøbing",
      "jobTypes": ["fuldtid"],
      "boostJob": true,
      "publishedDate": "12-03-2026",
      "applicationDeadline": "10-04-2026",
      "url": "https://jobdanmark.dk/job/it-chef-soeges-til-rah",
      "slug": "it-chef-soeges-til-rah",
      "company": "Rah Service A/S",
      "location": "Ringkøbing",
      "date": "2026-03-12",
      "deadline": "2026-04-10"
    }
  ]
}
```

> **Notes**:
> - `url` is normalized to a full URL (CLI prepends `https://jobdanmark.dk` to the relative path from the API).
> - `slug` is extracted from the relative `url` field (the path segment after `/job/`).
> - `applicationDeadline` can be `null`.
> - `publishedDate` format: `"DD-MM-YYYY"`.
> - Presentation-only keys the API sends (`coverImage`, `companyLogo`, `companyLogoSvgMarkup`, `overlayColor`, `silhouetteLogo`) are dropped from search output — they were ~40% of a live payload and an agent can never use them.
> - Every result also carries the cross-portal contract fields `company`, `location`, `date` and `deadline`, derived from `companyName`, the city after the postal code in `companyAddress`, and the day-first dates converted to `YYYY-MM-DD` — `/scrape` Step 2 expects search output to include title, company, location, date, and URL. Native fields are preserved unchanged.

### `detail` — Full job posting detail

**Method**: Fetch HTML from `https://jobdanmark.dk/job/{slug}`. The CLI extracts a `<script type="application/ld+json">` JobPosting block when present, and falls back to parsing the rendered job page HTML when Jobdanmark omits JSON-LD.

```bash
bun run src/cli.ts detail <slug> [--format json|plain]
```

The `slug` is the URL path segment returned as `slug` in `search` results (e.g. `it-chef-soeges-til-rah`).

#### Example

```bash
bun run src/cli.ts detail it-chef-soeges-til-rah
bun run src/cli.ts detail it-chef-soeges-til-rah --format plain
```

#### Response shape

```json
{
  "slug": "it-chef-soeges-til-rah",
  "url": "https://jobdanmark.dk/job/it-chef-soeges-til-rah",
  "title": "IT-chef søges til RAH",
  "datePosted": "2026-03-12",
  "validThrough": "2026-04-10",
  "employmentType": ["FULL_TIME"],
  "hiringOrganization": {
    "name": "Rah Service A/S",
    "logo": "https://jobdanmark.dk/media/k1epc2kk/rah-service-logo.jpg?width=50"
  },
  "jobLocation": {
    "streetAddress": "Ndr Ringvej 4 6950 Ringkøbing",
    "addressLocality": "Ringkøbing",
    "addressRegion": "Vestjylland",
    "postalCode": "6950",
    "addressCountry": "DK"
  },
  "description": "<p>Full HTML description...</p>",
  "applyUrl": "https://example.com/apply"
}
```

> **Note**: The `hiringOrganization.logo`, `validThrough`, and `applyUrl` fields may be `null` if not present in the structured data or rendered page. `jobLocation` fields may be `null` if the location data is absent.

### `categories` — List categories with live job counts

**Endpoint**: `GET https://jobdanmark.dk/api/categorycount/getcounts`

```bash
bun run src/cli.ts categories [flags]
```

#### Flags

| Flag | Type | Default | Description |
|------|------|---------|-------------|
| `--limit` | number | — | Cap number of categories returned |
| `--format` | string | `json` | Output format: `json`, `table`, `plain` |

### `autocomplete` — Suggest job titles and categories

**Endpoint**: `GET https://jobdanmark.dk/api/search/autocomplete?q={query}`

```bash
bun run src/cli.ts autocomplete --query <text> [flags]
```

#### Flags

| Flag | Type | Default | Description |
|------|------|---------|-------------|
| `--query` | string | **required** | Search text to autocomplete |
| `--limit` | number | — | Cap total suggestions returned |
| `--format` | string | `json` | Output format: `json`, `table`, `plain` |

> **Note**: Returns an empty array `[]` when no suggestions match. `value` is a numeric ID usable as `--jobtitle-id` or `--category` in `search`. Groups with no matching items are omitted from the response.

### `locations` — Suggest location filters

**Endpoint**: `GET https://jobdanmark.dk/api/search/locations?q={query}`

```bash
bun run src/cli.ts locations --query <text> [flags]
```

#### Flags

| Flag | Type | Default | Description |
|------|------|---------|-------------|
| `--query` | string | **required** | Location text to search (city, zip code, region) |
| `--limit` | number | — | Cap total suggestions returned |
| `--format` | string | `json` | Output format: `json`, `table`, `plain` |

> **Note**: Returns `[]` when no locations match. The `value` field for municipalities is the display name (string); for zip codes it is the numeric string. Pass `value` to the `search` flag matching its `category`: `--municipality`, `--zip`, `--region` (category `region`, a landsdel such as `Sjælland`) or `--administrative-region` (category `administrativeRegion`, such as `Region Midtjylland`). Under any other flag the value matches no jobs.

---

## 📊 指标

### Job Types

| Value | Danish | English |
|-------|--------|---------|
| `fuldtid` | Fuldtid | Full-time |
| `deltid` | Deltid | Part-time |
| `fleksjob` | Fleksjob | Flex job |
| `elev` | Elev | Apprentice |
| `studiejob` | Studiejob | Student job |
| `praktik` | Praktik | Internship |

### Categories

| ID | Danish Title |
|----|-------------|
| 227972 | Pædagogik, Uddannelse og Forskning |
| 227973 | Håndværk, Industri, Transport og Landbrug |
| 227974 | Salg, Kommunikation, Marketing, og Design |
| 227975 | Pleje, Social og Sundhed |
| 227976 | Hotel, Service, Restauration og Sikkerhed |
| 227977 | Kontor, Finans og Økonomi |
| 227978 | IT, Ingeniør og Energi |
| 227979 | Ledelse, HR og projektstyring |
| 543415 | Kirke, Kultur og Underholdning |
| 227980 | Øvrige job |

---

## 🏛️ 设计

### Error handling

All errors are written to **stderr** in JSON format and exit with code `1`:

```json
{ "error": "Job not found", "code": "NOT_FOUND" }
{ "error": "API request failed: 400 Bad Request", "code": "API_ERROR" }
{ "error": "--query is required", "code": "MISSING_REQUIRED" }
{ "error": "Failed to parse job page HTML", "code": "PARSE_ERROR" }
```

### URL construction

- Job detail pages: `https://jobdanmark.dk/job/{slug}`
- Image URLs from the raw API (`companyLogo.url`, `coverImage.url`) are relative; prepend `https://jobdanmark.dk` if you consume the API directly (the CLI drops these keys)

---

## 📁 目录

「待补充」

---

## 🔗 资源

- **Job Portal**: [Jobdanmark.dk](https://www.jobdanmark.dk)
- **API Base**: `https://jobdanmark.dk`

---

## 🚀 快速开始

```bash
cd .agents/skills/jobdanmark-search/cli
bun install
```

---

## ⚙️ 技术栈

- **Runtime**: Bun
- **Language**: TypeScript
- **Data Format**: JSON (API), HTML with JSON-LD (detail pages)