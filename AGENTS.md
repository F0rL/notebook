# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an **Obsidian 个人文档知识库** (personal knowledge management vault), not a code project. It contains markdown documentation covering three main domains:

- **大模型/** - Large Language Model (LLM) concepts, skills, and terminology
- **后端开发/** - Backend development notes (Node.js, databases, DevOps)
- **前端开发/** - Frontend ecosystem tool summaries (React, Vue)

## Document Quality Rules

### 生成文档时必须检查

- **文字错误**: 错别字、标点错误、语法错误
- **乱码**: 特殊字符显示异常、编码问题
- **格式一致性**: 标题层级、列表格式、代码块语法

### 笔记属性 (Frontmatter) - 必填

每次创建或修改文档时，**必须**在文件顶部添加 Obsidian YAML frontmatter：

```yaml
---
tags:
  - 标签1
  - 标签2
created: 2026-05-14
updated: 2026-05-14
author: kuma
---
```

**必填字段说明**:

- `tags`: 至少包含 2-3 个相关标签
- `created`: 文档创建日期 (YYYY-MM-DD)
- `updated`: 文档最后更新日期 (YYYY-MM-DD)
- `author`: 作者名

**常用标签规范**:
| 分类 | 标签示例 |
|------|----------|
| 内容类型 | `#学习笔记`, `#技术文档`, `#工具汇总`, `#知识整理` |
| 技术栈 | `#react`, `#vue`, `#nodejs`, `#docker`, `#mysql`, `#llm` |
| 工具 | `#obsidian`, `#cursor`, `#claude-code`, `#skill` |
| 用途 | `#速查`, `#入门`, `#进阶`, `#���佳实践` |

**标签命名规范**:

- 使用小写字母
- 多词用短横线连接 (如 `#前端开发`)
- 避免中文标签混用，优先使用英文技术标签

## Interaction Guidelines

- This is a **documentation-only repository** - no build/test commands apply
- When user asks about technologies mentioned in these docs, reference the relevant files
- The markdown files contain up-to-date (2026) technology recommendations and comparisons
- Use Context7 MCP tool to fetch current documentation for any external libraries
- **Always** add proper frontmatter tags when creating or modifying documents
- Always verify document quality (spelling, encoding) before completing any document task

## No Code Present

This repository contains **no executable code** (no `.js`, `.ts`, `.json` config files, etc.). It's a personal knowledge management vault in Obsidian format.
