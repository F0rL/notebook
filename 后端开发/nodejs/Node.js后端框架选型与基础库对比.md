---
tags:
  - nodejs
  - 后端
  - 框架选型
  - 工具汇总
created: 2026-05-14
updated: 2026-06-30
author: kuma
---

# Node.js 后端框架选型与基础库对比

## 主流框架对比

| 特性 | Express 5 | Fastify 5 | NestJS 11 | Hono 4 | Koa | Egg.js |
|------|-----------|-----------|-----------|--------|-----|--------|
| **作者/维护** | 社区 (OpenJS) | 社区 (NearForm, Matteo Collina) | Kamil Mysliwiec | 社区 (yusukebe) | 社区 (TJ 等) | 阿里 |
| **首次发布** | 2010 | 2016 | 2017 | 2022 | 2013 | 2016 |
| **最新大版本** | v5.1 (2024) | v5.x (2024) | v11 (2025) | v4.x (2025) | v2.x | v3.x |
| **TypeScript** | 部分支持 (需 @types) | 内置一流支持 | 强制使用 TS | 原生 TS 设计 | 部分支持 (需 @types) | 部分支持 (需 @types) |
| **架构风格** | 中间件 (线性) | 中间件 + 插件 + JSON Schema | MVC + DI + 模块化 | 中间件 (Web Standard) | 中间件 (洋葱模型) | MVC + 约定优于配置 |
| **性能 (基准)** | 中等 (~10K req/s) | 高 (~15-20K req/s) | 取决于适配器 | 极高 (~62K req/s on Node) | 较好 | 中等 |
| **运行时** | Node.js | Node.js | Node.js | Node, Bun, Deno, CF Workers, Vercel Edge, AWS Lambda | Node.js | Node.js |
| **插件生态** | 极其丰富 (最大) | 丰富 | 丰富 (模块系统) | 快速成长 | 较丰富 | 丰富 (国内) |
| **学习曲线** | 低 | 中 | 高 (需 DI 知识) | 低 | 低-中 | 中 |
| **路由** | 手动注册 | 声明式 | 装饰器 + 声明式 | 声明式 (Radix Trie) | 手动注册 | 声明式 + 自动路由 |
| **OpenAPI 文档** | 需第三方库 | 内置 (@fastify/swagger) | 需插件 (@nestjs/swagger) | 需第三方 | 需第三方 | 需插件 |
| **适用场景** | 已有项目维护、简单 API | 高性能 API、微服务 | 大型企业级项目 (>3人团队) | Edge/Serverless、跨运行时 API | 中小型项目 | 大型企业级项目 (国内) |

### 框架选型建议

- **Express 5**: 生态最成熟、中间件最丰富、招聘池最大。v5.1 修复了异步错误处理的长期痛点。**2026 年不再推荐新项目使用**，但现有 Express 项目升级到 v5 是合理选择。适合团队已有 Express 经验或需要最大兼容性的场景。
- **Fastify 5**: 性能最强（比 Express 快 2-3 倍），内置 JSON Schema 验证 + 序列化 + 自动 Swagger 文档，插件系统完善。**2026 年新 Node.js API 的首选推荐**。适合高性能 API 网关、微服务和重视吞吐量的场景。
- **NestJS 11**: 企业级框架，Angular 风格模块化 + 依赖注入，内置 gRPC/Kafka/消息队列传输层。**大型团队（8+ 人）的默认选择**。可切换底层适配器（Express 或 Fastify），使用 Fastify 适配器可获得接近原生的性能。适合复杂业务系统、微服务体系。
- **Hono 4**: 2026 年最值得关注的新星。基于 Web Standard (Fetch API)，一套代码运行于 Node/Bun/Deno/Cloudflare Workers/Vercel Edge/AWS Lambda。核心约 14KB，性能可达 62K req/s。内置 JWT/CORS/日志等中间件，RPC 客户端支持端到端类型安全。适合 Edge 部署、Serverless 函数、跨运行时 API。
- **Koa**: 比 Express 更现代的中间件模型（async/await + 洋葱模型），但生态和活跃度持续下降。新项目不建议选择，被 Hono 和 Fastify 取代。
- **Egg.js**: 阿里企业级框架，约定优于配置，国内生态完善。社区活跃度下降，新项目更推荐 NestJS（国内也开始流行）。

### 2026 选型决策矩阵

| 场景 | 推荐框架 | 理由 |
|------|----------|------|
| 新 Node.js API（中小团队） | **Fastify 5** | 最佳性能 + 开发体验平衡，内置验证和文档 |
| 新 Node.js API（大型团队 8+ 人） | **NestJS 11** (Fastify 适配器) | DI + 模块化在规模化团队中回报显著 |
| Edge / Serverless | **Hono 4** | 跨运行时、极小体积、冷启动极快 |
| 已有 Express 项目 | 保持 Express，升级到 5.x | 迁移成本通常大于收益 |
| 已有 NestJS 项目 | 升级到 NestJS 11 | v11 对齐了 Express 5 / Fastify 5 |

---

## 常用基础库对比

### ORM / 数据库操作

| 库 | 类型 | TypeScript | 支持数据库 | 周下载量 | 特点 |
|---|------|------------|-----------|----------|------|
| **Prisma 6** | ORM | ✅ 一流支持 | PostgreSQL, MySQL, SQLite, SQL Server, MongoDB | ~8M | 声明式 schema（schema.prisma）、自动生成类型客户端、迁移工具最佳。v6 改进性能，Prisma Accelerate 提供 Edge 兼容路径 |
| **Drizzle ORM** | ORM / Query Builder | ✅ 一流支持（无代码生成） | PostgreSQL, MySQL, SQLite, Turso, D1 | ~4M (快速增长) | SQL-like 语法，零代码生成，纯 JS/TS 无二进制依赖，Edge 原生支持。性能比 Prisma 快 2-3 倍。2026 增长最快的 ORM |
| **Kysely** | Query Builder | ✅ 优秀 | PostgreSQL, MySQL, SQLite | 中等 | 纯 SQL Query Builder，类型安全，最接近原生 SQL 的开发体验。适合需要精细 SQL 控制的场景 |
| **TypeORM** | ORM | ✅ | MySQL, PostgreSQL, SQLite, MongoDB, Oracle 等 | ~3M | 装饰器驱动，支持 DataMapper 和 ActiveRecord 模式。维护活跃度持续下降，新项目不推荐 |
| **Sequelize** | ORM | ⚠️ @types | MySQL, PostgreSQL, SQLite, MSSQL | 高 | 老牌 ORM，生态成熟，但 TS 支持和 API 设计已落后于现代方案 |
| **Mongoose** | ODM | ⚠️ @types | MongoDB | 高 | MongoDB 专属 ODM，提供 Schema 校验、中间件、虚拟字段。MongoDB 项目仍首选 |
| **Knex.js** | Query Builder | ⚠️ @types | PostgreSQL, MySQL, SQLite, MSSQL | 高 | 老牌 SQL Query Builder，灵活度最高，可配合 Objection.js 使用。新项目更推荐 Kysely |

**推荐**:
- 大多数团队首选 **Prisma**（最佳 DX + 迁移工具）
- SQL 优先团队 / Edge 部署首选 **Drizzle ORM**（性能 + 类型安全 + Edge 兼容）
- 需要精细 SQL 控制首选 **Kysely**
- MongoDB 项目首选 **Mongoose**
- **TypeORM 不再推荐新项目使用**

### 数据验证

| 库 | 类型 | 特点 |
|---|------|------|
| **Zod 4** | Schema 验证 | 2026 年**事实标准**。v4 比 v3 快 14 倍、体积减少 57%。月下载量 15M+。生态最大（tRPC、Hono、React Hook Form、Drizzle 均有集成） |
| **Valibot** | Schema 验证 | 模块化 / 函数式 API，按需导入实现极致 Tree-Shaking。简单校验可 <1KB。前端表单和 Edge 部署首选 |
| **TypeBox** | Schema 验证 | JSON Schema 原生，配合 AJV 编译运行时最快。Fastify 官方推荐，适合需要 OpenAPI/JSON Schema 生成的 API 场景 |
| **ArkType** | Schema 验证 | TS 语法即校验 (`{ name: 'string.email' }`)，类型推断最精准，运行时极快 |
| **class-validator** | 装饰器验证 | 配合 NestJS 使用最佳，与 class-transformer 组合。注意：维护活跃度有所下降 |
| **Ajv** | JSON Schema 验证 | Fastify 内置，高性能 JSON Schema 编译验证，适合高吞吐场景 |
| **Joi** | Schema 验证 | 功能强大，但 TS 支持弱。新项目不推荐，保留给 hapi.js 或已有 Joi 生态项目 |
| **Yup** | Schema 验证 | 类 Joi API，前端使用更多。新项目推荐 Zod/Valibot 替代 |

**推荐**: 通用场景 **Zod 4**，前端/Edge **Valibot**，API/OpenAPI 场景 **TypeBox**，NestJS 场景 **class-validator**。

### 认证与授权

| 库 | 类型 | 特点 |
|---|------|------|
| **better-auth** | 认证框架 | **2026 年新项目首选**。TypeScript 原生，插件架构（Passkeys/MFA/OAuth/Magic Links/Organization），框架适配器（Next.js, SvelteKit, Nuxt, Hono, Express 等）。周下载量从 50K 飙升至 500K+ |
| **Auth.js v5** (原 NextAuth) | 认证框架 | Next.js 生态标准，80+ OAuth 提供商，v5 支持多框架（SvelteKit, Nuxt, SolidStart）。周下载量 1.5M+。适合重度依赖 OAuth 或企业 SSO 的项目 |
| **Passport.js** | 认证中间件 | Express 时代标准，500+ 策略。**2026 年不再活跃维护**但仍在广泛使用。适合已有 Express + Passport 的维护项目 |
| **jsonwebtoken** | JWT 工具 | 底层 JWT 签发/验证，DIY 场景基础库。注意：需自行实现 refresh token、黑名单等机制 |
| **jose** | JWT/JWS/JWE 工具 | 新的 JWT 标准库（Filip Skokan），API 更现代，支持 Edge Runtime。推荐替代 jsonwebtoken |
| **bcrypt / bcryptjs** | 密码哈希 | 密码加密存储标准方案。原生 bcrypt 更快，bcryptjs 纯 JS 无编译依赖 |
| **Lucia v3** | 认证库 (已归档) | ⚠️ **2025 年 3 月进入维护模式，不推荐新项目使用**。现已转为认证架构学习资源 |

**推荐**: 新项目 **better-auth**（功能最完整），Next.js 重度 OAuth 场景 **Auth.js v5**，简单 DIY 场景 **jose + bcrypt**，已有 Express 项目 **Passport.js**（保持不迁移）。

### 日志

| 库 | 特点 |
|---|------|
| **Pino** | **2026 年新项目默认推荐**。性能最佳（比 Winston 快 5-10 倍），Worker Thread 传输不阻塞事件循环，Fastify 默认日志库，JSON 优先。周下载量快速追赶 Winston |
| **Winston** | 历史最流行的日志库（周下载量最高），传输最多（80+ 社区传输），格式灵活，配置丰富。适合已有 Winston 项目或需要丰富传输的场景 |
| **pino-http** | HTTP 日志中间件，Pino 生态，替代 Morgan，周下载量 3M+ |
| **Morgan** | HTTP 请求日志中间件（Express 常用），轻量简洁。新项目可考虑 pino-http 替代 |
| **LogTape** | 新型零依赖日志库，支持 Node / 浏览器 / Edge 全运行时，Tree-Shakeable。适合库作者或跨运行时项目 |
| **log4js** | 类 log4j 配置，支持多 Appender，国内老项目使用较多，新项目不推荐 |

**推荐**: 新项目 **Pino**（性能优先），已有 Winston 项目**无需迁移**（除非日志成为瓶颈），跨运行时 **LogTape**。

### HTTP 客户端

| 库 | 特点 |
|---|------|
| **Axios** | 最流行（周下载量最高），拦截器、自动 JSON 解析、取消请求。稳定可靠，生态最成熟 |
| **undici** | Node.js 官方团队维护，性能极优，底层 fetch 实现。Node >= 20 可用全局 fetch 替代简单场景 |
| **ofetch** | 基于 fetch API，轻量，现代设计，unjs 生态（Nuxt 默认）。API 简洁 |
| **got** | 功能丰富，支持 hooks、重试、流式传输。适合需要高级 HTTP 功能的场景 |

**推荐**: 通用场景 **Axios**（最成熟），追求轻量 **ofetch**，高性能场景 **undici**。Node >= 20 简单请求可直接用内置 `fetch`。

### 配置管理

| 库 | 特点 |
|---|------|
| **Node.js --env-file** | **Node 20.6+ 内置**，原生 .env 文件加载，零依赖。不支持变量展开（`${HOST}:${PORT}`）。v24.10 起稳定 |
| **dotenv** | 最轻量的 .env 文件加载库，支持 dotenv-expand 变量展开。仍需时代码式加载 |
| **Zod + dotenv** | **2026 年推荐方案**：dotenv 加载 + Zod 校验和类型推导，一石二鸟 |
| **@nestjs/config** | NestJS 官方配置模块，支持 .env、自定义 validate 函数 |
| **convict** | Mozilla 出品，功能全面的配置管理：schema 定义、类型校验、环境变量映射、CLI 参数。v6.2.5 (2026.3 更新)，仍活跃维护 |
| **node-config** | 多环境配置文件组织（default/production/development.json），传统方案 |

**推荐**: 简单场景 **Node.js --env-file**（零依赖）或 **dotenv + Zod**（TS 项目标准），NestJS 场景 **@nestjs/config**，复杂配置需求 **convict**。

### 队列 / 消息

| 库 | 特点 |
|---|------|
| **BullMQ** | Redis 驱动，支持延迟/重复/优先级任务、速率限制、Web UI Dashboard。**Node.js 后台任务首选**，部署简单（仅需 Redis） |
| **KafkaJS** | Apache Kafka 客户端，适合高吞吐事件流（百万级 TPS）、事件溯源、数据管道。架构更复杂 |
| **amqplib (RabbitMQ)** | AMQP 协议客户端，适合需要精确路由的消息队列场景（Exchange/Queue 模式）。复杂度介于 BullMQ 和 Kafka 之间 |
| **Bee-Queue** | 轻量 Redis 队列，API 简洁。功能较 BullMQ 少 |
| **Agenda** | MongoDB 驱动，持久化任务调度。适合已在用 MongoDB 的项目 |

**推荐**: 后台任务 **BullMQ**，事件流 / 大数据管道 **KafkaJS**，复杂消息路由 **RabbitMQ/amqplib**。

### 测试

| 库 | 类型 | 特点 |
|---|------|------|
| **Vitest 4** | 测试框架 | **2026 年新项目默认推荐**。Vite 驱动，兼容 Jest API，冷启动快 3-5x，Watch 模式快 28x，原生 ESM/TS。周下载量 40M+ 且持续增长。State of JS 2025 满意度排名第一 |
| **Jest 30** | 测试框架 | 最广泛安装（Meta 维护），快照/覆盖率/Mock 一站式。v30 支持了原生 ESM。适合已有 Jest 项目或 React Native 项目（Vitest 不支持 RN） |
| **Playwright** | E2E 测试 | **E2E 测试绝对领先**。微软出品，多浏览器，自动等待、Trace Viewer。State of JS 2025 满意度 91% vs Cypress 72% |
| **Supertest** | HTTP 集成测试 | 配合测试框架对 HTTP 接口进行集成测试，轻量实用 |
| **node:test** | Node 内置 | Node 20+ 稳定，零依赖，适合库/CLI 工具测试。缺少 Watch 模式、快照、UI，不适合大型应用测试 |

**推荐**: 单元测试 **Vitest**（新项目默认），React Native **Jest**，E2E **Playwright**，工具库/CLI **node:test**，HTTP 测试 **Supertest**。

### 进程管理

| 工具 | 特点 |
|------|------|
| **Docker / Docker Compose** | **2026 年现代部署标准**。容器化隔离、环境一致性、CI/CD 天然集成、水平扩展。配合 K8s/ECS 实现编排 |
| **PM2** | 传统 VPS 部署首选。自动重启、零停机重载、负载均衡、日志管理。适合单机或少量服务器场景 |
| **Cluster (内置)** | Node.js 内置多进程模块，手动实现。通常配合 PM2 使用（PM2 Cluster Mode） |
| **Docker + PM2** | 在 Docker 容器内使用 PM2 Cluster Mode 以利用多核。注意：Docker 推荐一容器一进程模式，复杂场景可用此组合 |

**推荐**: **Docker**（现代推荐方案），传统 VPS **PM2**（简单够用），单机单应用 Docker 直接运行即可。

---

## 2026 推荐技术栈组合

### 方案 A：快速起步 (中小型项目 / 创业团队)

> **Fastify 5 + Prisma 6 + Zod 4 + Pino + Axios + dotenv + BullMQ + Vitest 4 + Docker**

### 方案 B：企业级标准 (大型项目 / 多团队)

> **NestJS 11 (Fastify 适配器) + Prisma 6 + class-validator + Pino + Axios + @nestjs/config + BullMQ + Vitest 4 + Docker / K8s**

### 方案 C：Edge / Serverless (跨运行时 / 全球低延迟)

> **Hono 4 + Drizzle ORM + Valibot (或 Zod 4-mini) + Pino + ofetch + BullMQ (Redis) + Vitest 4**

### 方案 D：轻量高性能 API (微服务 / API 网关)

> **Fastify 5 + Drizzle ORM + TypeBox + Pino + undici + dotenv + KafkaJS + Vitest 4 + Docker / K8s**

---

> **选题建议**: 方案 A（Fastify + Prisma）适合大多数 2026 年新项目；方案 B（NestJS）适合需要强制架构统一的大型团队；方案 C（Hono）适合拥抱现代运行时的前沿项目。
