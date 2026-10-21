---
tags:
  - react
  - 前端
  - 工具汇总
  - frontend
created: 2026-05-14
updated: 2026-05-14
author: kuma
---

# React 生态

> 截至 2026 年 5 月，React 19 为最新稳定版。

## 核心

| 工具 | 说明 |
|------|------|
| **React 19** | 最新稳定版，新增 Server Components、Actions、use() hook |
| **React 18** | 上一版本，引入并发特性、Suspense、useId |
| **React Native** | 用 React 构建原生移动应用 |

## 元框架 / 全栈

| 框架 | 说明 |
|------|------|
| **Next.js 16** | React 官方推荐的全栈框架，App Router、RSC、Server Actions |
| **Remix** | Web 标准优先的全栈框架，嵌套路由、数据加载 |
| **Gatsby** | 静态站点生成器，React 生态 (不如 Next.js 活跃) |
| **Expo** | React Native 开发的官方推荐框架，EAS 云构建/热更新 |

## 构建工具

| 工具 | 说明 |
|------|------|
| **Vite** | 主流构建工具，React + TypeScript 项目首选 |
| **Turbopack** | 基于 Rust 的增量打包器 (Next.js 内置) |
| **Rspack** | 字节跳动出品的 Rust 打包器，webpack 兼容 |

## 状态管理

| 库 | 说明 |
|----|------|
| **Zustand** | 轻量级状态管理库 (目前最流行)，API 简洁，无 Provider 包裹 |
| **Redux Toolkit** | 官方推荐的 Redux 方式，内置 immer/thunk/实体适配器 |
| **Jotai** | 原子状态管理，灵感来自 Recoil，按需重渲染 |
| **MobX** | 基于观察者的响应式状态管理，自动追踪依赖 |
| **TanStack Query** | 服务端状态管理 (缓存/同步/重请求)，React 开发必备 |
| **Zustand + Valtio** | 同作者出品，Valtio 基于 Proxy 实现响应式 |
| **Recoil** | Meta 出品 (已归档，不推荐新项目使用) |

## 路由

| 库 | 说明 |
|----|------|
| **React Router v7** | 社区最主流的路由库，支持 loader/action/嵌套路由 |
| **TanStack Router** | 类型安全的 React 路由，支持文件路由、懒加载 |

## UI 组件库

| 库 | 说明 |
|----|------|
| **shadcn/ui** | 2025-2026 最流行的 UI 方案，基于 Radix UI，组件源码可定制 |
| **Ant Design** | 国内最主流的企业级中后台组件库，React 版 |
| **Material UI (MUI)** | Google Material Design 实现，组件最全 |
| **Mantine** | 功能丰富的 React 组件库，hooks 完善 |
| **Chakra UI** | 设计系统友好，可访问性优秀 |
| **NextUI** | 基于 Tailwind CSS 的 React UI 库，美观现代 |
| **Radix UI** | 无样式可访问 UI 基元 (shadcn/ui 的底层依赖) |
| **RainbowKit** | 链上连接钱包 React UI 组件 (以太坊 dApp) |
| **Headless UI** | 无样式可访问 UI 组件 (Tailwind CSS 团队出品) |
| **Ark UI** | 组件即 API，支持 React/Vue/Solid，含 Figma 设计 |

## 数据请求

| 库 | 说明 |
|----|------|
| **TanStack Query** | React 数据请求首选，自动缓存/乐观更新/SSR |
| **SWR** | Vercel 出品，基于 stale-while-revalidate 策略 |
| **RTK Query** | Redux Toolkit 内置，与 Redux 集成度高 |
| **Apollo Client** | GraphQL 客户端，缓存/订阅/分片 |
| **urql** | 轻量级 GraphQL 客户端，可扩展 |

## 表单

| 库 | 说明 |
|----|------|
| **React Hook Form** | 非受控表单之王，性能最佳，API 简洁 |
| **Conform** | 基于表单原生约束 + Zod 校验的渐进式表单库 |
| **Formik** | 经典表单库 (维护模式，不推荐新项目) |

## 表单校验

| 库 | 说明 |
|----|------|
| **Zod** | TypeScript 优先的 Schema 校验库，与 React Hook Form 集成 |
| **Yup** | 对象 Schema 校验 (较老，不如 Zod 流行) |

## 样式 / CSS

| 方案 | 说明 |
|------|------|
| **Tailwind CSS** | 目前最主流的原子 CSS 框架 |
| **CSS Modules** | CRA/Vite 原生支持，零配置 |
| **Panda CSS** | 编译时原子 CSS，输出类型安全 |
| **styled-components** | CSS-in-JS 方案 (使用率逐渐下降) |
| **Vanilla Extract** | 编译时 CSS-in-JS，类型安全 |

## 图表 / 可视化

| 库 | 说明 |
|----|------|
| **Recharts** | React 原生图表，基于 D3，声明式 API |
| **ECharts** | Apache 顶级项目，功能最全 (react-echarts 封装) |
| **@antv/g2** | 蚂蚁集团，图形语法，可定制性高 |
| **visx** | Airbnb 出品，基于 D3 的 React 可视化基元 |
| **nivo** | 基于 D3 的 React 图表，提供好看的默认主题 |
| **@ant-design/charts** | Ant Design 图表的 React 封装 |

## 表格 / 数据网格

| 库 | 说明 |
|----|------|
| **TanStack Table** | Headless 表格，支持排序/筛选/分组/虚拟滚动 |
| **react-data-grid** | 类 Excel 数据网格组件 |

## 动画

| 库 | 说明 |
|----|------|
| **Framer Motion** | React 动画库的事实标准，声明式 API，motion 组件 |
| **react-spring** | 基于弹簧物理的动画库 |
| **GSAP** | 专业级动画引擎，不限于 React |
| **Motion (motion.dev)** | Framer Motion 作者的新作，更轻量 |

## 测试

| 工具 | 说明 |
|------|------|
| **Vitest** | 基于 Vite 的测试框架，兼容 Jest 语法 |
| **React Testing Library** | 官方推荐的组件测试方案 (而非测试实现细节) |
| **Playwright** | 微软出品的 E2E 测试框架，多浏览器支持 |
| **Storybook** | 组件探索/UI 测试/视觉回归测试 |
| **MSW** | Mock 请求拦截，浏览器/Node 双模式 |
| **Cypress** | 开发者友好的 E2E 测试框架 |

## 开发工具

| 工具 | 说明 |
|------|------|
| **React DevTools** | 浏览器扩展，调试组件树/Props/State/Profiler |
| **ESLint-plugin-react** | React ESLint 规则集 |
| **ESLint-plugin-react-hooks** | Hooks 使用规则检查 (必须安装) |
| **VS Code ES7+ React/Redux** | VS Code 代码片段扩展 |
| **Biome** | Rust 实现的格式化 + Lint 工具 (替代 ESLint + Prettier) |
| **Turborepo** | 前端 Monorepo 构建编排工具 |
| **pnpm workspace** | Node.js 包管理器，Monorepo 支持好 |

## 跨平台 / 桌面

| 工具 | 说明 |
|------|------|
| **Tauri** | Rust 后端 + Web 前端桌面应用框架，体积小 |
| **Electron** | 成熟桌面应用框架 (体积大) |
| **React Native + Expo** | 用 React 编写原生 iOS/Android 应用 |
| **Tamagui** | React Native + Web 统一 UI 方案 |
| **Solito** | React Native + Next.js 统一路由方案 |

## 脚手架

| 工具 | 说明 |
|------|------|
| **create-next-app** | Next.js 官方脚手架 |
| **create-vite** | Vite 通用脚手架，选 React 模板 |
| **create-t3-app** | TypeScript、Tailwind、tRPC、NextAuth 一体化脚手架 |
| **create-expo-app** | Expo 应用脚手架 |

## 其他值得关注的库

| 库 | 说明 |
|----|------|
| **Zod** | TypeScript Schema 校验，React 生态通用层 |
| **tRPC** | 端到端类型安全的 API 构建 (前后端类型共享) |
| **react-hook-form + Zod** | 表单 + 校验是目前最佳实践组合 |
| **TanStack Router** | 类型安全路由的新选择 |
| **Valibot** | Zod 的轻量替代 (包体积更小) |
| **Drizzle ORM** | 类型安全的 Node.js ORM，常与 tRPC 搭配 |
