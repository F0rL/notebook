---
tags:
  - claude-code
  - 工具汇总
  - 速查
  - 效率
created: 2026-05-15
updated: 2026-05-15
author: kuma
---

# Claude Code 指令速查

> Claude Code (claude.ai/code) 常用命令与技能速查手册，兼顾日常开发与新手指南。

---

## 一、斜杠命令 (Slash Commands)

在对话输入框中直接输入以下命令：

### 1.1 核心命令

| 命令 | 说明 | 示例 |
|------|------|------|
| `/help` | 获取帮助信息，查看可用命令列表 | 直接输入 `/help` |
| `/model` | 切换 AI 模型 | 查看/切换当前模型 |
| `/config` | 打开配置 (主题、模型等设置) | 配置 Claude Code |
| `/clear` | 清空当前对话会话 | 重新开始对话 |
| `/compact` | 压缩对话上下文，减少 token 消耗 | 优化长对话 |

### 1.2 任务与循环

| 命令 | 说明 | 示例 |
|------|------|------|
| `/loop` | 设置循环任务，按间隔重复执行 | `/loop 5m /check` 每5分钟执行 |
| `/remember` | 保存重要信息到记忆系统 | `/remember 用户名是 kuma` |
| `/forget` | 删除记忆中的指定信息 | `/forget 用户名` |

### 1.3 Git 操作

| 命令 | 说明 | 示例 |
|------|------|------|
| `/git` | 快速执行 Git 操作 | `/git status` |
| `/pr` | 创建或查看 Pull Request | `/pr create` |
| `/branch` | 管理 Git 分支 | `/branch list` |

### 1.4 开发辅助

| 命令 | 说明 | 示例 |
|------|------|------|
| `/test` | 生成测试代码 | `/test 编写单元测试` |
| `/review` | 代码审查 | `/review 当前改动` |
| `/explain` | 解释代码功能 | `/explain 这段代码` |
| `/refactor` | 代码重构建议 | `/refactor 重构这个函数` |

### 1.5 搜索与导航

| 命令 | 说明 | 示例 |
|------|------|------|
| `/find` | 搜索文件或代码 | `/find functionName` |
| `/goto` | 跳转到指定文件或行 | `/goto src/main.ts:10` |
| `/web` | 搜索网络获取信息 | `/web React 19 新特性` |

### 1.6 自定义命令

创建自定义斜杠命令，存放在 `~/.claude/commands/` 目录：

```markdown
# ~/.claude/commands/build.md
---
description: 构建项目
allowed-tools: Bash
---

执行项目构建:
!`npm run build`
```

---

## 二、常用技能 (Skills)

使用 `Skill` 工具调用，或在对话中提及自动触发。

### 2.1 开发流程类

| 技能 | 触发场景 | 说明 |
|------|----------|------|
| **brainstorming** | 新功能、组件、设计之前 | 头脑风暴，理清需求与设计方案 |
| **writing-plans** | 有明确需求/规格时 | 编写多步骤实施计划 |
| **executing-plans** | 执行计划时 | 按计划逐步实施 |
| **test-driven-development (TDD)** | 对质量要求高的功能 | 先写测试 → 测试失败 → 实现 → 重构 |

### 2.2 代码协作类

| 技能 | 触发场景 | 说明 |
|------|----------|------|
| **commit-push-pr** | 提交代码时 | 提交、推送并创建 PR |
| **code-review** | 审查代码时 | 全面代码审查 |
| **receiving-code-review** | 接收审查反馈时 | 系统处理审查意见 |

### 2.3 调试与优化类

| 技能 | 触发场景 | 说明 |
|------|----------|------|
| **systematic-debugging** | 修 Bug 时 | 结构化调试方法定位根因 |
| **simplify** | 代码优化时 | 审查修改代码，复用、质量、效率 |
| **verification-before-completion** | 任务完成前 | 自动检查需求满足、边界覆盖 |

### 2.4 前端与 UI 类

| 技能 | 触发场景 | 说明 |
|------|----------|------|
| **frontend-design** | 创建前端界面 | 高质量、有设计感的组件/页面 |
| **ui-ux-pro-max** | UI/UX 设计 | 50+ 风格、161 配色、99 UX 准则 |

### 2.5 工程效率类

| 技能 | 触发场景 | 说明 |
|------|----------|------|
| **dispatching-parallel-agents** | 多任务并行 | 调度多个 Agent 并行执行 |
| **subagent-driven-development** | 大型功能拆分 | 子 Agent 驱动开发 |
| **using-git-worktrees** | 多分支并行开发 | Git worktree 隔离环境 |

---

## 三、快捷键 (Keyboard Shortcuts)

### 3.1 核心操作

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+Enter` | 提交当前输入 |
| `Tab` | 接受自动补全建议 |
| `Escape` | 取消当前操作 |

### 3.2 对话操作

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+Shift+O` | 打开项目文件树 |
| `Ctrl+K` | 打开命令面板 (Command Palette) |
| `Ctrl+L` | 清空对话 |
| `Ctrl+S` | 保存当前会话 (部分环境) |

### 3.3 编辑操作

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+Z` | 撤销 |
| `Ctrl+Y` | 重做 |
| `Ctrl+/` | 切换注释 |
| `Alt+↑/↓` | 移动行 |

---

## 四、文件操作工具

### 4.1 读取与搜索

| 工具 | 用途 | 示例 |
|------|------|------|
| **Read** | 读取文件内容 | `Read file_path: "src/index.ts"` |
| **Glob** | 按模式搜索文件 | `Glob pattern: "**/*.ts"` |
| **Grep** | 搜索文件内容 | `Grep path: "src" pattern: "TODO"` |
| **LSP** | 代码跳转/引用 | `LSP operation: "goToDefinition"` |

### 4.2 编辑与创建

| 工具 | 用途 | 示例 |
|------|------|------|
| **Edit** | 修改文件 (替换) | `Edit file_path, old_string, new_string` |
| **Write** | 创建/重写文件 | `Write file_path, content` |
| **NotebookEdit** | 编辑 Jupyter | `NotebookEdit notebook_path, cell_id` |

### 4.3 执行与调试

| 工具 | 用途 | 示例 |
|------|------|------|
| **Bash** | 执行 Shell 命令 | `Bash command: "npm run build"` |
| **PowerShell** | 执行 PowerShell | `PowerShell command: "Get-Process"` |
| **Agent** | 调度子任务 | `Agent subagent_type: "Explore"` |

---

## 五、MCP 工具 (Model Context Protocol)

### 5.1 常用 MCP 服务

| 服务 | 用途 |
|------|------|
| **context7** | 获取最新文档 (React, Next.js, Prisma 等) |
| **playwright** | 浏览器自动化测试与操作 |

### 5.2 Playwright 浏览器操作

| 工具 | 说明 |
|------|------|
| `browser_navigate` | 导航到 URL |
| `browser_click` | 点击元素 |
| `browser_type` | 输入文本 |
| `browser_snapshot` | 获取页面快照 |
| `browser_screenshot` | 截图 |
| `browser_console_messages` | 获取控制台消息 |

### 5.3 Context7 文档查询

```bash
# 查询 React 文档
mcp__plugin_context7_context7__resolve-library-id libraryName: "React"
mcp__plugin_context7_context7__query-docs libraryId: "/facebook/react" query: "useEffect cleanup"
```

---

## 六、常用工作流

### 6.1 快速修复 Bug

```
systematic-debugging → 定位问题 → 修复 → verification-before-completion
```

### 6.2 开发新功能

```
brainstorming → 设计方案 → writing-plans → 执行计划 → 代码审查
```

### 6.3 前端页面开发

```
brainstorming → ui-ux-pro-max → 前端实现 → 验证
```

### 6.4 提交代码

```
完成开发 → commit-push-pr → requesting-code-review → code-review → 合并
```

---

## 七、模型选择指南

| 场景 | 推荐模型 |
|------|----------|
| 日常对话、快速编码 | Haiku |
| 复杂推理、代码架构 | Sonnet |
| 深度思考、超高要求 | Opus |
| 中国语境、中文优化 | GLM (glm-5.1) |

---

## 八、实战技巧

### 8.1 高效提问

```markdown
# 推荐格式
## 目标
我要实现什么功能

## 上下文
当前代码在哪个文件，有什么约束

## 已尝试
我试过什么方法，结果如何

# 示例
## 目标
修复用户登录时 session 丢失的问题
## 上下文
使用 Express + express-session，部署在 Docker
## 已尝试
检查了 session 配置，清理了缓存，问题依旧
```

### 8.2 利用记忆功能

- `/remember <内容>` - 保存重要信息到记忆
- 未来对话可自动调用相关记忆

### 8.3 任务管理

```bash
# 创建任务
TaskCreate subject: "修复登录 Bug" description: "session 问题"

# 查看任务
TaskList

# 更新任务
TaskUpdate taskId: "1" status: "in_progress"
```

---

## 九、快速参考表

| 需求 | 操作 |
|------|------|
| 查看文件 | `Read` 工具 |
| 搜索代码 | `Grep` 工具 |
| 搜索文件 | `Glob` 工具 |
| 执行命令 | `Bash` / `PowerShell` |
| 创建文件 | `Write` 工具 |
| 修改代码 | `Edit` 工具 |
| 新建任务 | `TaskCreate` |
| 查看任务 | `TaskList` |
| 切模型 | `/model` |
| 循环任务 | `/loop` |
| 获取帮助 | `/help` |

---

> **提示**：大多数技能可以自动触发，只需在对话中描述你的需求，Claude Code 会自动选择合适的技能。