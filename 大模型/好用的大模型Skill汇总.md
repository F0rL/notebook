---
tags:
  - claude-code
  - skill
  - 工具汇总
created: 2026-05-14
updated: 2026-05-14
author: kuma
---

# 好用的大模型 Skill 汇总

> 整理时间：2026-05-14

---

## 一、通用开发流程类

### 1. brainstorming（头脑风暴）
在开始任何创造性工作（新功能、组件、架构设计）之前使用。帮助你理清需求、设计思路、探索多种方案后再动手写代码。**建议在动手实现前优先调用。**

### 2. writing-plans（编写计划）
当你有了明确的需求或 spec，需要制定多步骤的实施计划时使用。会在动手写代码前先产出完整方案，避免走弯路。

### 3. executing-plans（执行计划）
配合 writing-plans 使用，负责按计划逐步执行实施步骤。

### 4. test-driven-development（TDD 测试驱动开发）
严格遵循"先写测试→测试失败→实现代码→测试通过→重构"的循环。适用于对质量要求较高的功能开发。

### 5. systematic-debugging（系统化调试）
面对 Bug 时，用结构化方法定位根因而非靠直觉猜测。包含问题复现、假设验证、二分定位等步骤。

### 6. verification-before-completion（完成前验证）
在宣告任务完成前，自动检查是否所有需求都满足、边界情况是否覆盖、是否有回归问题。

7.skills
为多个工具统一管理[skills](https://www.npmjs.com/package/skills)

---

## 二、代码协作类

### 7. code-review（代码审查）
对 Pull Request 进行全面的代码审查，覆盖逻辑正确性、安全性、性能、可维护性等维度。

### 8. requesting-code-review（请求代码审查）
当你即将提交代码时，帮助你准备好待审查的内容，包括 PR 描述、变更摘要等。

### 9. receiving-code-review（接收代码审查反馈）
帮助你系统地处理审查意见，逐条分析、修改、回复。

### 10. commit / commit-push-pr（提交与 PR 流程）
规范化地创建 commit、推送并创建 Pull Request，自动遵循项目的 commit message 风格。

---

## 三、前端与 UI/UX 类

### 11. frontend-design（前端设计）
创建高质量、有设计感的前端界面。避免千篇一律的 AI 风格，产出有独特美感的组件和页面。适用于网页组件、页面、Dashboard 等。

### 12. ui-ux-pro-max（UI/UX 专业设计）
涵盖 50+ 设计风格、161 套配色方案、57 种字体搭配、99 条 UX 准则。支持 React、Vue、Next.js、Tailwind、shadcn/ui 等主流技术栈。适合从零设计或优化现有 UI。

---

## 四、代码质量类

### 13. simplify（代码精简优化）
审查修改过的代码，检查复用性、质量、效率问题，并自动修复。适合在功能开发完成后做一次整体优化。

### 14. security-review（安全审查）
对代码进行安全审计，检查 OWASP Top 10 相关漏洞（注入攻击、XSS、认证缺陷等）。

---

## 五、工程效率类

### 15. dispatching-parallel-agents（并行调度）
当有 2 个以上互不依赖的任务时，自动将它们分派给多个 Agent 并行执行，大幅缩短总耗时。

### 16. subagent-driven-development（子代理驱动开发）
在实现计划中将各独立任务分配给子 Agent 并行完成，适合大型功能拆分。

### 17. using-git-worktrees（Git Worktree 管理）
使用 git worktree 在隔离环境中并行开发多个分支，互不干扰。

### 18. finishing-a-development-branch（完成开发分支）
功能开发完毕、测试通过后，引导你选择合适的收尾方式（合并、PR、清理等）。

---

## 六、项目配置类

### 19. claude-md-management（CLAUDE.md 管理）
根据当前会话的积累，自动更新项目的 CLAUDE.md 文件，沉淀项目上下文、规范和约定。

### 20. claude-md-improver（CLAUDE.md 改进）
优化现有的 CLAUDE.md 内容，使其更精准、更有用。

### 21. init（初始化 CLAUDE.md）
为新项目自动生成 CLAUDE.md 文档，包含项目结构、技术栈、约定等关键信息。

### 22. skill-creator（Skill 创建器）
创建、修改和优化自定义 Skill。当你需要封装团队特有的工作流时使用。

---

## 七、API 与集成类

### 23. claude-api（Claude API 开发）
针对 Anthropic SDK / Claude API 的专项支持，涵盖 prompt caching、thinking、tool use、模型迁移（4.5→4.6→4.7）等特性。

### 24. mcp-builder（MCP 服务构建）
构建 MCP（Model Context Protocol）服务器，让 Claude 能连接外部工具和数据源。

---

## 八、运维与其他

### 25. review（PR Review）
快速审查 Pull Request，给出结构化的反馈。

### 26. clean_gone（清理远程已删分支）
清理本地那些远程已删除但仍存在的 [gone] 分支及其关联的 worktree。

### 27. ralph-loop（循环任务）
按指定的时间间隔自动重复执行某个任务或命令，适合持续监控、定期检查等场景。

### 28. fewer-permission-prompts（减少权限弹窗）
分析你的操作记录，自动生成常用工具的 allowlist 配置，减少重复的权限确认弹窗。

---

## 快速选择指南

| 场景 | 推荐 Skill |
|------|-----------|
| 新功能开发 | brainstorming → writing-plans → executing-plans |
| 高质量要求 | brainstorming → TDD → simplify |
| 前端页面 | brainstorming → ui-ux-pro-max / frontend-design |
| 修 Bug | systematic-debugging |
| 提交代码 | commit-push-pr → requesting-code-review |
| 审查代码 | code-review |
| 项目初始化 | init |
| API 开发 | claude-api |
| 安全审计 | security-review |
| 多任务加速 | dispatching-parallel-agents |
