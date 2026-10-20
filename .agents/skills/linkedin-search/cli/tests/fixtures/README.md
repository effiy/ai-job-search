---
title: LinkedIn Detail Fixture
tags: [test, fixture, linkedin, detail, html]
category: 测试
created: 2026-07-14
updated: 「待补充」
source: https://github.com/MadsLorentzen/ai-job-search/pull/153
type: 测试数据
status: 活跃
lifecycle: 稳定
---

# LinkedIn detail fixture

---

## 💡 概述

`detail-orbis-excerpt.html` contains verbatim excerpts from the guest response for
posting `4345841651`, captured by **portapulito** through this CLI's `htmlFetch`
on 2026-07-14 and published in [upstream PR #153](https://github.com/MadsLorentzen/ai-job-search/pull/153).
That PR was closed because it was accidentally submitted to upstream instead of
the author's personalized fork; it was not merged.

Source: `.agents/skills/linkedin-search/cli/test/__fixtures__/detail-orbis.html`
at commit `5bc3afb94d19b670ecf09287c20d0c95e5d13ba0`.
Original SHA-256: `2125a346c22f471e3db08cd3890d0569b194703fe5f49b831cdde257e4aa4a59`.

Only the title, closed banner, description markup and criteria list are retained.
Their bytes are unmodified; recruiter cards, images, tracking links and unrelated
page sections are omitted. The description includes real adjacent paragraphs,
empty paragraphs and list items, rather than hand-written parser examples.

The CLI regression serves this saved response over loopback and redirects only
the exact expected guest endpoint to it in a child process. It makes no LinkedIn
requests and does not regenerate the fixture during tests.

---

## ⚡ 功能

- Serves a saved HTML fixture for CLI regression tests without making real LinkedIn requests
- Provides realistic test data including adjacent paragraphs, empty paragraphs, and list items
- Tests run offline with no network access required

---

## 📊 指标

「待补充」

---

## 🏛️ 设计

The CLI regression serves this saved response over loopback and redirects only
the exact expected guest endpoint to it in a child process.

---

## 📁 目录

「待补充」

---

## 🔗 资源

- **Upstream PR**: [#153](https://github.com/MadsLorentzen/ai-job-search/pull/153)
- **Source Commit**: `5bc3afb94d19b670ecf09287c20d0c95e5d13ba0`
- **Original SHA-256**: `2125a346c22f471e3db08cd3890d0569b194703fe5f49b831cdde257e4aa4a59`

---

## 🚀 快速开始

「待补充」

---

## ⚙️ 技术栈

- **Test Data**: HTML fixture (LinkedIn guest response)
- **Test Runner**: Bun
- **Approach**: Loopback redirect in child process (no real network calls during tests)