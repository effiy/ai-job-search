---
title: Custom Templates
tags: [templates, latex, typst, cv, cover-letter]
category: 模板
created: 「待补充」
updated: 「待补充」
source: 「待补充」
type: 目录说明
status: 活跃
lifecycle: 稳定
---

# Custom Templates

---

## 💡 概述

This folder holds user-registered templates (LaTeX, Typst, or any other toolchain with a declared compile command), managed by the `/add-template` command. The framework works out of the box with its stock templates (moderncv for CVs, `cover.cls` for cover letters) — this folder only gets content when you register your own.

---

## ⚡ 功能

- `/add-template` interviews you for the template's instructions (source extension, compile command, fonts, style rules, page limit), stores the files here, and runs a mandatory test compile before registering anything.
- Activating a template adds a managed block to `05-cv-templates.md` or `06-cover-letter-templates.md`, which is what `/apply` reads when drafting and compiling — no other wiring needed.
- `/add-template --list` shows registered templates; `/add-template --use <name>` switches; `/add-template --use default` reverts to the stock templates.

Templates are stored with `[PLACEHOLDER]` tokens instead of personal data, so they are safe to commit and share.

---

## 📊 指标

「待补充」

---

## 🏛️ 设计

### Layout

```
templates/
├── cv/
│   └── <template-name>/
│       ├── template.<ext>  # Profile-agnostic skeleton ([PLACEHOLDER] tokens), e.g. template.tex or template.typ
│       ├── TEMPLATE.md      # Manifest: source extension, compile command, fonts, page limit, style rules, pitfalls
│       ├── *.cls / *.sty    # Custom class/style files, or Typst packages (if the template needs them)
│       └── fonts/           # Bundled font files (if not using system fonts)
└── cover_letters/
    └── <template-name>/
        └── (same layout)
```

---

## 📁 目录

```
templates/
├── cv/                         # CV templates
│   └── <template-name>/        # Individual template directory
└── cover_letters/              # Cover letter templates
    └── <template-name>/        # Individual template directory
```

---

## 🔗 资源

「待补充」

---

## 🚀 快速开始

Run `/add-template` and follow the interactive interview to register a new template. Point it at your source file (a `.tex` file plus any `.cls`/`.sty` files or bundled fonts; a `.typ` file plus any local packages; or an equivalent for another toolchain).

---

## ⚙️ 技术栈

- **Supported Toolchains**: LaTeX, Typst, or any command-line PDF compiler
- **Stock CV Template**: moderncv (banking style)
- **Stock Cover Letter Template**: custom cover.cls (Lato/Raleway fonts)