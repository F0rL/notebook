---
tags:
  - vue
  - 前端
  - 工具汇总
  - frontend
created: 2026-05-14
updated: 2026-05-14
author: kuma
---

# Vue 生态

## 核心

- **Vue 3**: https://vuejs.org/
- **Vue 2**: https://v2.vuejs.org/ (已停止维护)

## 构建工具

| 工具 | 说明 |
|------|------|
| **Vite** | 下一代前端构建工具，Vue 团队官方推荐，极速 HMR |
| **Rolldown** | Vite 团队开发的 Rust 打包器，未来将替换 Rollup |

## 元框架 / 全栈

| 框架 | 说明 |
|------|------|
| **Nuxt 4** | Vue 官方全栈框架，SSR/SSG/SWR、自动路由、服务端 API |
| **VitePress** | 基于 Vite + Vue 的静态站点生成器，适合文档站 |
| **quasar** | 一套代码同时构建 SPA、SSR、PWA、Mobile App、Electron 桌面应用 |

## 状态管理

| 工具 | 说明 |
|------|------|
| **Pinia** | Vue 官方推荐的状态管理库，TypeScript 友好，替代 Vuex |
| **Vuex** | Vue 2 时代的官方状态管理 (Vue 3 不推荐) |
| **tanstack/vue-query** | 服务端状态管理，自动缓存/同步/重请求 |

## 路由

| 工具 | 说明 |
|------|------|
| **Vue Router** | Vue 官方路由，支持嵌套路由、导航守卫、懒加载 |

## UI 组件库

| 库                     | 说明                                    |
| --------------------- | ------------------------------------- |
| **Element Plus**      | 国内最流行的 Vue 3 组件库，企业级中后台首选             |
| **Naive UI**          | Vue 3 组件库，TypeScript 优先，设计现代          |
| **Ant Design Vue**    | Ant Design 的 Vue 3 实现，与 React 版设计一致   |
| **Vuetify**           | Material Design 组件库，功能极其丰富            |
| **PrimeVue**          | 功能全面的 Vue 3 组件库，提供 styled/unstyled 模式 |
| **shadcn-vue**        | shadcn/ui 的 Vue 版本，Radix Vue 生态       |
| **Arco Design Vue**   | 字节跳动出品，中后台 UI 解决方案                    |
| **TDesign Vue Next**  | 腾讯出品，企业级设计体系                          |
| **Headless UI (Vue)** | 无样式、完全可访问的 UI 基元组件                    |

## 移动端 / 跨平台

| 工具 | 说明 |
|------|------|
| **uni-app** | 一套代码发布到 10+ 平台 (H5、微信/支付宝/抖音小程序、App) |
| **Taro** | 京东出品，支持 React/Vue 的跨端开发框架 |
| **Ionic Vue** | 基于 Web Components 的移动端 UI + 原生能力 |
| **Capacitor** | 跨平台原生运行时，可配合 Vue 构建移动 App |
| **Electron + Vue** | 搭配 electron-builder 或 electron-vite 构建桌面应用 |

## 样式 / CSS

| 工具 | 说明 |
|------|------|
| **UnoCSS** | 即时按需原子 CSS 引擎，比 Tailwind 更灵活更快 |
| **Tailwind CSS** | 原子 CSS 框架，Vue 生态广泛使用 |
| **Windi CSS** | 与 Tailwind 兼容的按需 CSS 引擎 (UnoCSS 的前身) |

## Hooks / 组合式工具

| 工具 | 说明 |
|------|------|
| **VueUse** | 200+ 个组合式 API 工具函数集合，Vue 开发必备 |

## 数据请求

| 库 | 说明 |
|----|------|
| **Axios** | HTTP 客户端，拦截器/请求取消/超时控制 |
| **ofetch** | Nuxt 团队开发的轻量 HTTP 客户端 |

## 表单

| 工具 | 说明 |
|------|------|
| **vee-validate** | Vue 表单验证库，支持 yup/zod 集成 |
| **FormKit** | 一站式表单构建框架，schema 驱动 |

## 图表 / 可视化

| 库 | 说明 |
|----|------|
| **ECharts** | Apache 顶级项目，国内最主流的图表库 |
| **AntV (G2)** | 蚂蚁集团出品，面向数据可视化的图形语法引擎 |
| **@antv/s2** | 多维交叉分析表格 |
| **vue-chartjs** | Chart.js 的 Vue 封装 |

## 国际化

| 库 | 说明 |
|----|------|
| **vue-i18n** | Vue 官方国际化方案 |

## 测试

| 工具 | 说明 |
|------|------|
| **Vitest** | 基于 Vite 的单元测试框架，与 Jest 兼容 |
| **Vue Test Utils** | Vue 组件单元测试官方工具集 |
| **Playwright** | 微软出品的 E2E 测试框架，支持多浏览器 |
| **Cypress** | 开发者友好的 E2E 测试框架 |

## 开发工具

| 工具 | 说明 |
|------|------|
| **Vue DevTools** | 浏览器扩展，调试组件树/状态/事件/Pinia |
| **Vue Language Features (Volar)** | VS Code 插件，Vue 语法高亮/类型提示/模板智能感知 |
| **ESLint plugin-vue** | Vue 官方 ESLint 规则集 |
| **Storybook** | UI 组件探索与文档工具 |

## Node.js 运行时

| 工具 | 说明 |
|------|------|
| **tsx** | TypeScript 文件直接执行器 (替代 ts-node) |
| **unbuild** | Nuxt 团队的通用构建工具，适合库/工具开发 |

## 脚手架 / 模板

| 工具 | 说明 |
|------|------|
| **create-vue** | Vue 官方脚手架 (pnpm create vue) |
| **nuxt init** | Nuxt 项目初始化 |
| **vitesse** | 社区流行的 Vue 3 + Vite 起手模板 |


