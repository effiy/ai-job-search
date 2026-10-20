---
title: linkedin-cli
tags: [cli, linkedin, job-search, global, bun]
category: 工具
created: 「待补充」
updated: 「待补充」
source: https://www.linkedin.com
type: CLI工具
status: 活跃
lifecycle: 稳定
---

# linkedin-cli

CLI for searching jobs on LinkedIn's public job listings, for **any country/region**
(and remote), across any sector.

**Data source**: LinkedIn `jobs-guest` endpoints (`seeMoreJobPostings/search` and `jobPosting/<id>`).
**Authentication**: None required.
**Dependencies**: None (plain `bun` + `fetch`). `bun install` is optional and only pulls dev type defs.

> **Personal use only.** This uses LinkedIn's public job pages; automated access is against
> LinkedIn's Terms of Service. Keep volume low, don't use it commercially or for bulk data
> collection, and run it on your own responsibility.

---

## 💡 概述

A zero-dependency Bun-based CLI tool for searching LinkedIn's public job listings across any country or region. Uses unauthenticated `jobs-guest` endpoints to search and retrieve job postings.

---

## ⚡ 功能

### Commands

| Command | Description |
|---------|-------------|
| `search` | Search for job listings (`--location` required) |
| `detail` | Fetch full detail for a single job listing |

`search` accepts `--format json|table|plain` (default `json`); `detail` accepts `--format json|plain`.
All errors are written to **stderr** as `{ "error": "...", "code": "..." }` with exit code `1`.

### Search flags

| Flag | Alias | Description |
|------|-------|-------------|
| `--location` | `-l` | **Required.** Place string, e.g. `"Mumbai, Maharashtra, India"`, `"Berlin, Germany"`, `"Remote"`. |
| `--query` | `-q` | Keywords (title / skill / role). Recommended. |
| `--jobage` | | Posted within N days: `1`, `7`, `14`, `30`. |
| `--remote` | | `remote` \| `hybrid` \| `onsite`. |
| `--page` | | 1-indexed page (10 results/page). |
| `--limit` | `-n` | Cap results emitted. |
| `--format` | | `json` \| `table` \| `plain`. |

### Quick examples

```bash
# Software roles in Hyderabad, last 7 days
bun run src/cli.ts search -q "backend engineer" -l "Hyderabad, Telangana, India" --jobage 7 --format table

# Design roles in London
bun run src/cli.ts search -q "product designer" -l "London, United Kingdom" --format table

# Fully remote
bun run src/cli.ts search -q "technical writer" -l "Remote" --remote remote --format table

# Full detail for one job
bun run src/cli.ts detail 4426311357 --format plain
```

See `../SKILL.md` for the full flag reference and the Terms-of-Service note.

---

## 📊 指标

「待补充」

---

## 🏛️ 设计

「待补充」

---

## 📁 目录

「待补充」

---

## 🔗 资源

- **LinkedIn Jobs**: [https://www.linkedin.com/jobs](https://www.linkedin.com/jobs)
- **Skill Documentation**: `../SKILL.md`

---

## 🚀 快速开始

```bash
cd .agents/skills/linkedin-search/cli
bun install   # optional — only installs TypeScript dev types
```

The CLI runs without any install because it has zero runtime dependencies.

---

## ⚙️ 技术栈

- **Runtime**: Bun
- **Language**: TypeScript
- **Dependencies**: Zero runtime dependencies
- **Data Source**: LinkedIn `jobs-guest` public endpoints