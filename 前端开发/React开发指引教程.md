---
tags:
  - react
  - frontend
  - 技术文档
  - 学习路径
created: 2026-09-21
updated: 2026-09-22
author: kuma
---

# React 开发指引教程

> 面向 Vue3 开发者的 React 系统教程。版本快照：**2026-09-22**，基线为 [React 19.3](https://react.dev/blog/2026/09/09/react-19-3)、[React Compiler 1.0](https://react.dev/blog/2025/10/07/react-compiler-1)、[Next.js 16.3](https://nextjs.org/blog/next-16-3)、[Vite 8](https://vite.dev/blog/announcing-vite8)、[React Router 8](https://reactrouter.com/8.0.0/start/changelog)、Tailwind CSS 4、Ant Design 6.6。
> 本文按"心智模型 → 核心语法 → Hooks → 数据与状态 → 框架与工程化"的主线组织，目标是读完即可上手真实项目。各库的完整速查清单见 [[React生态工具汇总]]，Vue 侧对照见 [[Vue生态工具汇总]]。

## 学习路径总览

| 阶段 | 目标 | 对应章节 |
|------|------|----------|
| 1. 换脑子 | 理解 React 与 Vue 更新机制的本质差异 | 心智模型 |
| 2. 跑起来 | 建项目、写组件、过 JSX 这一关 | 起步 / JSX 与组件 |
| 3. 核心 | 熟练使用 Hooks，能写出自定义 Hook | Hooks 核心心法 |
| 4. 新范式 | 掌握 React 19 的 Actions、use()、Compiler | React 19 新范式 |
| 5. 数据层 | 服务端状态与全局状态的现代方案 | 数据请求 / 状态管理 |
| 6. 工程 | 路由、样式、表单、框架选型 | 路由 / 样式 / 表单 / 全栈框架 |
| 7. 成熟 | 工具链、测试、避坑，形成自己的脚手架 | 工程化 / 测试 / 常见坑 |

每个阶段 2-5 天即可，有 Vue3 基础的开发者通常 3-4 周能达到生产级熟练度。

## 心智模型：从 Vue3 到 React

Vue 与 React 最深的差异不在语法，而在更新机制。Vue 3 基于响应式代理：`ref()`、`reactive()` 被修改时，框架通过依赖收集精确知道"哪些组件用了这个数据"，只更新它们。React 没有响应式系统：调用 setState 后，React 从该组件开始重渲染整个子树，再用虚拟 DOM diff 算出真实 DOM 的最小改动。所以 React 代码风格围绕两件事展开：状态不可变更新、以及用 memo 手段减少多余重渲染（React Compiler 1.0 之后大部分交给编译器自动做）。

| 维度     | Vue 3                       | React 19                               |
| ------ | --------------------------- | -------------------------------------- |
| 视图描述   | 模板（SFC `<template>`）        | JSX（在 `.tsx` 里直接写 JS）                  |
| 更新机制   | 响应式依赖收集，精准更新                | setState 触发重渲染 + diff                  |
| 状态定义   | `ref()` / `reactive()`，可直接改 | `useState`，必须整体替换                      |
| 派生数据   | `computed()`                | 渲染中直接计算，或 `useMemo`（Compiler 可自动处理）    |
| 副作用    | `watch()` / `watchEffect()` | `useEffect`                            |
| 跨层传值   | `provide` / `inject`        | `useContext`（`<Context>` 直接当 Provider） |
| 子传父    | `emit` 事件                   | 传回调函数作为 prop                           |
| 双向绑定   | `v-model`                   | `value` + `onChange` 受控组件              |
| 插槽     | `slot` / 具名插槽               | `children` / 传组件当 prop                 |
| 条件渲染   | `v-if` / `v-show`           | `&&`、三元、`<Activity>`（保留状态）             |
| 列表渲染   | `v-for`                     | `.map()` + `key`                       |
| 作用域样式  | `<style scoped>`            | CSS Modules / Tailwind / 组件库           |
| 全局状态   | Pinia                       | Zustand / Redux Toolkit / Jotai        |
| 路由     | Vue Router                  | React Router 8 / Next.js App Router    |
| 逻辑复用   | Composables（`useXxx`）       | 自定义 Hook（`useXxx`，几乎同名）                |
| 缓存组件   | `<KeepAlive>`               | `<Activity mode="hidden">`             |
| 传输 DOM | `<Teleport>`                | `createPortal`                         |
| 过渡动画   | `<Transition>`              | `<ViewTransition>` / Motion            |

五条最关键的思维转换：

1. **JSX 里没有指令系统**。没有 `v-if`、`v-for`、`v-model`，一切都是 JavaScript 表达式——这既是门槛也是自由度来源。
2. **状态不可变**。`state.items.push(x)` 在 React 里是错的，必须 `setItems([...items, x])` 产生新引用，React 靠引用变化才知道要更新。
3. **派生数据不用缓存思维**。Vue 习惯给所有派生数据套 `computed`；React 推荐渲染中直接算，把缓存留给编译器。
4. **Effect 是同步外部的最后手段**。Vue 里 `watch` 很常用，React 官方立场是 "You Might Not Need an Effect"——能推导出来的数据不要用 effect 存。
5. **更新是调度而非即时**。setState 之后读到的还是旧值（批处理、异步调度），需要新值时用函数式更新 `setX(prev => ...)`。

## 起步：三条项目路线

2026 年启动一个 React 项目有三条主流路线，先选路线再动手：

| 路线 | 命令 | 适用场景 |
|------|------|----------|
| A. Vite 纯前端 SPA | `npm create vite@latest my-app -- --template react-ts` | 中后台系统、嵌入已有后端的页面、学习 React 本身 |
| B. Next.js 全栈 | `npx create-next-app@latest` | 需要 SSR/SEO 的网站、SaaS 产品、全栈一体项目 |
| C. TanStack Start | `npm create @tanstack/start@latest` | 想要全栈能力但更贴近纯 Vite 心智、类型安全优先 |

```bash
# 路线 A：Vite 8 + React 19 + TypeScript（Rolldown 已并入 Vite 8 主线，构建提速明显）
npm create vite@latest my-app -- --template react-ts
cd my-app && npm install && npm run dev
```

Vite 路线的典型目录约定：

```text
src/
├── components/        # 通用组件
├── features/          # 按业务域拆分（推荐代替按类型拆分）
│   └── order/
│       ├── components/
│       ├── hooks/     # 该域的自定义 Hook
│       └── api/       # 该域的请求封装
├── hooks/             # 跨域自定义 Hook
├── lib/               # 工具函数、fetch 封装
├── stores/            # Zustand 等 store
├── App.tsx
└── main.tsx
```

与 Vue 的 SFC 不同，React 没有 `.vue` 这样的单文件组件格式，一个组件就是一个 `.tsx` 文件：函数 + JSX 返回值。样式、类型、逻辑都在同一个文件里，社区约定用 "hooks 放文件顶部、子组件放同文件底部" 的模式组织。

## JSX 与组件基础

JSX 是"在 JS 里写 HTML"，Vue 模板是"在 HTML 里写 JS 模板表达式"。对照着学最快：

```tsx
function ProductCard({ product, onAdd }: {
  product: Product
  onAdd: (id: number) => void   // 子传父：回调 prop，等价于 emit
}) {
  const inStock = product.stock > 0   // 普通变量，每次渲染重新计算

  return (
    <div className="card">
      {/* 条件渲染：&& 或三元，没有 v-if */}
      {product.tag && <span className="tag">{product.tag}</span>}
      <h3>{product.name}</h3>

      {/* 列表：map + key，key 必须稳定唯一，不要用数组下标 */}
      <ul>
        {product.specs.map(spec => (
          <li key={spec.id}>{spec.label}: {spec.value}</li>
        ))}
      </ul>

      {/* class 用 className，style 接收对象且属性是驼峰 */}
      <p className={inStock ? 'text-green' : 'text-gray'}
         style={{ fontSize: 14 }}>
        {inStock ? '有货' : '缺货'}
      </p>

      {/* 事件：小驼峰 + 箭头函数传参 */}
      <button onClick={() => onAdd(product.id)} disabled={!inStock}>
        加入购物车
      </button>
    </div>
  )
}
```

Vue 插槽与 React children 的对应：

```tsx
// Vue: <Modal><template #header>...</template>默认内容</Modal>
// React: 默认插槽就是 children prop，具名插槽就是传组件当 prop
function Modal({ children, header, footer }: {
  children: ReactNode
  header?: ReactNode
  footer?: ReactNode
}) {
  return (
    <div className="modal">
      <div className="modal-header">{header}</div>
      <div className="modal-body">{children}</div>
      <div className="modal-footer">{footer}</div>
    </div>
  )
}

// 使用
<Modal header={<h2>确认删除</h2>} footer={<Button>取消</Button>}>
  <p>该操作不可恢复</p>
</Modal>
```

props 的三条铁律：只读不可改、单向流动、变化时子组件重渲染。需要"双向"，就用受控组件模式——这是 React 版的 `v-model`：

```tsx
function SearchInput({ value, onChange }: {
  value: string
  onChange: (v: string) => void
}) {
  return (
    <input
      value={value}                      // 受控：值由父组件决定
      onChange={e => onChange(e.target.value)}
      placeholder="搜索商品"
    />
  )
}
```

其余基础差异：注释用 `{/* */}`；多根节点不需要像 Vue2 那样包一层，但需要指定 key 时用 `<Fragment key={id}>`；`v-html` 对应 `dangerouslySetInnerHTML`（慎用，防 XSS）。

## Hooks 核心心法

Hooks 是 React 的逻辑复用单元，对应 Vue 的 Composables。规则只有两条：只在函数组件或自定义 Hook 的**顶层**调用（不能放 if/循环里），只在 React 函数中调用。

### useState 与不可变更新

```tsx
function TodoList() {
  const [todos, setTodos] = useState<Todo[]>([])

  const addTodo = (text: string) => {
    // 必须产生新数组，push/splice 直接改引用不会触发更新
    setTodos(prev => [...prev, { id: crypto.randomUUID(), text, done: false }])
  }
  const toggle = (id: string) => {
    setTodos(prev => prev.map(t => t.id === id ? { ...t, done: !t.done } : t))
  }
  const remove = (id: string) => {
    setTodos(prev => prev.filter(t => t.id !== id))
  }
  // ...
}
```

对象嵌套深时手写展开很痛苦，工程上直接上 [immer](https://immerjs.github.io/immer/) 的 `produce` 或 Redux Toolkit 内置的 immer——写法回到"直接改"的直觉，但产物仍是不可变更新。

### useEffect：同步外部系统

`useEffect` 对应 `watch + onMounted + onUnmounted` 的合体，但 React 对它的定位更克制：**用来同步 React 之外的东西**（DOM API、订阅、定时器、日志），而不是组件内部的数据加工。

```tsx
function ChatRoom({ roomId }: { roomId: string }) {
  useEffect(() => {
    const conn = createConnection(roomId)
    conn.connect()
    return () => conn.disconnect()   // 清理函数，对应 onUnmounted，先于下次 effect 执行
  }, [roomId])                       // 依赖数组：这些值变了才重新执行
  // ...
}
```

三条实战纪律：

1. 依赖数组老老实实写全，由 `eslint-plugin-react-hooks` 检查，不要骗它。
2. 能在渲染中推导的数据不要用 effect + setState 存（会多一轮渲染），这是 Vue 开发者最容易带过来的习惯。
3. 开发环境 effect 会执行两次（StrictMode），这是故意暴露"没写清理函数"的 bug，不是异常。

### useMemo / useCallback 与 Compiler 时代

`useMemo` 对应 `computed`，`useCallback` 缓存函数引用。在 React Compiler（详见后文）启用后，**绝大多数手动 memo 不再需要写**——编译器自动分析依赖并缓存，代码回归"直接写"的朴素形态：

```tsx
// 开启 Compiler 后直接这样写，等价于旧手写 useMemo 的性能
function FilteredList({ items, keyword }: Props) {
  const filtered = items.filter(i => i.name.includes(keyword))
  const total = filtered.reduce((sum, i) => sum + i.price, 0)
  return <List items={filtered} total={total} />
}
```

未启用 Compiler 的存量项目里，它们仍是优化"昂贵计算"与"配合 memo 组件"的主要工具。

### useRef：可变值与 DOM 引用

```tsx
function VideoPlayer() {
  const videoRef = useRef<HTMLVideoElement>(null)   // 对应 Vue 模板引用
  const timerRef = useRef<number>(0)                // 存可变值：改 .current 不触发渲染

  const play = () => {
    videoRef.current?.play()
    timerRef.current = window.setInterval(tick, 1000)
  }
  return <video ref={videoRef} src="/demo.mp4" onPlay={play} />
}
```

记住一条分界线：**渲染过程要用的数据用 state，渲染过程不参与、只是记一笔的用 ref**（定时器 id、上一次的值、第三方实例）。

### useContext：provide/inject 的 React 版

React 19 起 `<Context>` 组件直接当 Provider 用，不再写 `.Provider`：

```tsx
const ThemeContext = createContext<'light' | 'dark'>('light')

// 根组件
<ThemeContext value={theme}>
  <App />
</ThemeContext>

// 任意深度的子组件
const theme = useContext(ThemeContext)
```

### useTransition 与 useDeferredValue

并发特性的入口：把更新标记为"可中断的低优先级"，对应"搜索输入时列表刷新不卡输入框"的场景。

```tsx
function SearchPage() {
  const [input, setInput] = useState('')
  const [query, setQuery] = useState('')
  const [isPending, startTransition] = useTransition()

  const onChange = (v: string) => {
    setInput(v)                          // 紧急：输入框立即响应
    startTransition(() => setQuery(v))   // 非紧急：结果列表可让路
  }
  // useDeferredValue 是另一种写法：让派生值"慢一拍"
  const deferredQuery = useDeferredValue(query)
}
```

### 自定义 Hook：React 版 Composable

封装"一段带状态的逻辑"，命名必须 `use` 开头。上文提到过的 `useWindowSize`：

```tsx
function useWindowSize() {
  const [size, setSize] = useState({ w: window.innerWidth, h: window.innerHeight })
  useEffect(() => {
    const onResize = () => setSize({ w: window.innerWidth, h: window.innerHeight })
    window.addEventListener('resize', onResize)
    return () => window.removeEventListener('resize', onResize)
  }, [])
  return size
}
```

与 VueUse 对应的社区工具库是 [ahooks](https://ahooks.js.org/) 与 [react-use](https://github.com/streamich/react-use)，提供现成的数百个 Hook。

## React 19 新范式

React 19 是近年变化最大的版本，2025-2026 年又通过 19.2、19.3 补齐了多块拼图，这些是 2026 年新项目的主力 API。

### Actions：表单与变更的现代写法

Actions 让"提交表单 → 等待 → 展示状态 → 乐观更新"不再需要手写一堆 isLoading。三个配套 Hook：

```tsx
import { useActionState, useFormStatus } from 'react'

function SignupPage() {
  // [状态, 提交函数, 是否等待中]
  const [error, submitAction, isPending] = useActionState(
    async (prevState, formData: FormData) => {
      const err = await api.signup(formData)
      return err ?? null            // 返回值会成为新的 state
    },
    null,
  )

  return (
    <form action={submitAction}>    {/* action prop 接函数，自动处理提交 */}
      <input name="email" type="email" required />
      <SubmitButton />
      {error && <p className="error">{error}</p>}
    </form>
  )
}

// 按钮的 pending 状态由离表单最近的提交自动提供
function SubmitButton() {
  const { pending } = useFormStatus()
  return <button disabled={pending}>{pending ? '提交中…' : '注册'}</button>
}
```

`useOptimistic` 处理乐观更新，对应"点赞先 +1，失败再回滚"：

```tsx
function LikeButton({ postId, likes }: { postId: number; likes: number }) {
  const [optimisticLikes, addOptimistic] = useOptimistic(
    likes,
    (current, amount: number) => current + amount,
  )
  return (
    <button onClick={async () => {
      addOptimistic(1)                  // 先乐观显示 +1
      await api.like(postId)            // action 完成后自动回滚到真实值
    }}>
      {optimisticLikes} 赞
    </button>
  )
}
```

### use()：在组件里直接读异步数据

`use()` 是首个可以在条件语句、循环里调用的 Hook，读取 Promise 或 Context，配合 Suspense 实现流式渲染：

```tsx
function Comments({ commentsPromise }: { commentsPromise: Promise<Comment[]> }) {
  const comments = use(commentsPromise)   // 挂起时由最近的 Suspense 展示 fallback
  return comments.map(c => <p key={c.id}>{c.text}</p>)
}

// 父组件先发起请求（水合前即开始），子组件再消费
<Suspense fallback={<Spinner />}>
  <Comments commentsPromise={fetchComments(postId)} />
</Suspense>
```

### ref 直接作为 prop 与 ref 清理函数

`forwardRef` 已被官方标记弃用——函数组件直接接收 `ref` prop。回调 ref 支持返回清理函数：

```tsx
function Input({ ref, ...props }: React.ComponentProps<'input'>) {
  return <input ref={ref} {...props} />
}
// 使用：<Input ref={inputRef} placeholder="姓名" />
```

### 19.2 / 19.3 的新组件

- `<Activity>`（19.2）：visible/hidden 两种模式，隐藏时**保留状态**且不渲染 DOM，是 `<KeepAlive>` 的官方对位方案。
- `useEffectEvent`（19.2 稳定）：从 effect 中抽出的"事件函数"，读取最新 props/state 但不进入依赖数组。
- `useId` 前缀改为 `_r_`（19.2 起，SSR 与视图过渡兼容需要），无感但别依赖旧前缀。
- `<ViewTransition>`（19.3 转正）：配合 `useTransition` 触发浏览器原生视图过渡动画，`addTransitionType` 可按导航方向定制动画。
- Fragment Refs（19.3）：`<Fragment ref>` 拿到一组 DOM 的句柄，可统一 focus/scrollIntoView。
- `browser()`（19.3）：`use(browser())` 一行让组件跳过服务端渲染，替代手写 mounted 状态。

### React Compiler：1.0 已稳定

[React Compiler](https://react.dev/learn/react-compiler/installation) 于 2025-10 发布 1.0，自动完成组件/hook 的记忆化，并让 `eslint-plugin-react-hooks` 的 recommended 规则集直接包含编译器视角的检查。启用方式：

```js
// Next.js 16：一行开启（16.3 还提供了实验性 Rust 版编译器，构建更快）
const nextConfig = { reactCompiler: true }

// Vite：babel-plugin-react-compiler
npm install -D babel-plugin-react-compiler
```

个别需要退出编译的组件加 `"use no memo"` 指令；装上 React DevTools 后，被编译器优化的组件会显示 Memo 徽章。结论：**2026 年新项目默认开启 Compiler，忘掉大部分手写 useMemo/useCallback**。

## 服务端状态与数据请求

React 社区把"组件状态"与"服务端数据的缓存"分开治理，这是与 Vue 生态（直接 axios + ref）最大的工程化差异。[TanStack Query](https://tanstack.com/query/latest)（v5 稳定主线）是服务端状态的事实标准：

```tsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'

function TodoList() {
  const queryClient = useQueryClient()

  // 注意：v5 只支持对象传参
  const { data, isPending, error } = useQuery({
    queryKey: ['todos'],                       // 缓存键
    queryFn: () => api.getTodos(),             // 请求函数
    staleTime: 60_000,                         // 1 分钟内视为新鲜，不重复请求
  })

  const addTodo = useMutation({
    mutationFn: (text: string) => api.createTodo(text),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['todos'] })  // 失效重取
    },
  })
  // ...
}
```

选型定位：

| 方案 | 定位 | 什么时候用 |
|------|------|------------|
| TanStack Query v5 | 服务端状态管理标准 | 默认选择：缓存、去重、后台刷新、乐观更新、无限滚动全覆盖 |
| SWR 2.5 | 轻量 hooks 数据请求 | 简单场景、Vercel 系项目 |
| RTK Query | Redux 全家桶内建 | 已有 Redux Toolkit 的大型项目 |
| RSC + Server Actions | 框架级方案 | Next.js/TanStack Start 项目中"首次渲染数据"，交互后变更仍常配 TanStack Query |
| Apollo Client 4 | GraphQL 客户端 | GraphQL 后端 |

客户端请求库本身用原生 `fetch` 或 ky/axios 皆可，TanStack Query 不关心底下是什么。

## 全局状态管理

| 库 | 最新版本 | 一句话定位 | 适用场景 |
|----|----------|------------|----------|
| Zustand | 5.x | 无 Provider、极简 API 的 store | **新项目默认**，从几行全局状态到中型应用都够用 |
| Redux Toolkit | 2.12 | 重规范、DevTools 生态最深 | 大型团队、严格时间旅行调试需求、存量 Redux 项目 |
| Jotai | 3.x | 原子化状态，自底向上组合 | 细粒度状态多的复杂界面（表格、设计工具） |
| Valtio | 2.3 | Proxy 可变写法 | 心智最接近 Vue，但社区活跃度已放缓 |

Zustand 最贴近"Pinia 心智"（单一 store、selector 精确订阅），推荐从这里入门：

```tsx
import { create } from 'zustand'
import { useShallow } from 'zustand/react/shallow'

interface CartStore {
  items: CartItem[]
  add: (item: CartItem) => void
  clear: () => void
}

export const useCart = create<CartStore>()((set) => ({
  items: [],
  add: (item) => set((s) => ({ items: [...s.items, item] })),
  clear: () => set({ items: [] }),
}))

// 组件中按字段订阅；返回对象时用 useShallow 避免多余重渲染
function CartBadge() {
  const items = useCart(useShallow((s) => s.items))
  return <span>{items.length}</span>
}
```

## 路由

### React Router 8（SPA/库模式主流）

2026-06 发布的 [v8 有个破坏性变化](https://reactrouter.com/8.0.0/start/changelog)：`react-router-dom` 包被移除，一切从 `react-router` 导入；并引入 RSC 支持（unstable 阶段）。网上大量 v6 教程的导入写法已过时：

```tsx
import { BrowserRouter, Routes, Route, Link, useParams, useNavigate } from 'react-router'

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/users/:id" element={<UserDetail />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  )
}

function UserDetail() {
  const { id } = useParams()
  const navigate = useNavigate()
  return <button onClick={() => navigate('/')}>返回</button>
}
```

### Next.js App Router（文件即路由）

```text
app/
├── layout.tsx          # 根布局（对应 Vue Router 的 App.vue 嵌套）
├── page.tsx            # / 路由
├── loading.tsx         # 该层级 Suspense fallback
├── error.tsx           # 错误边界
└── users/
    └── [id]/
        └── page.tsx    # /users/:id
```

### 选型

| 方案 | 场景 |
|------|------|
| React Router 8 | Vite SPA、需要 library 模式或框架模式的项目，社区默认 |
| Next.js App Router | Next.js 项目内，文件约定最成熟 |
| TanStack Router 1.x | 类型安全优先（路径参数/搜索参数全链路类型推导），可与 TanStack Start 组合 |

## 样式与组件库

Tailwind CSS v4 是当前绝对主流（4.x 已无 `tailwind.config.js`，CSS 一行引入），shadcn/ui 是"组件不装库、源码进项目"的新范式：

```css
/* app.css — Tailwind v4 */
@import "tailwindcss";
```

```tsx
// shadcn/ui：CLI 把组件源码复制进项目（基于 Radix UI 无障碍基元），随便改
npx shadcn@latest add dialog button

<Dialog>
  <DialogTrigger asChild>
    <Button variant="outline">打开</Button>
  </DialogTrigger>
  <DialogContent>...</DialogContent>
</Dialog>
```

选型全景：

| 方案 | 类型 | 适用场景 |
|------|------|----------|
| Tailwind CSS 4.3 + shadcn/ui | 原子 CSS + 拷贝式组件 | 新项目默认，To C 产品、设计自由度高的站 |
| Ant Design 6.6 | 企业级组件库 | 国内中后台系统的事实标准，原生兼容 React 19（无需 v5 时代的 patch 包） |
| MUI | Material Design 组件库 | 组件最全、国际化团队 |
| Mantine | 全功能组件库 + hooks | 独立开发、想要开箱即用又可定制 |
| CSS Modules | 打包器内建 | 简单项目、零依赖偏好 |
| styled-components / Emotion | CSS-in-JS | 已进入退潮期（RSC 兼容成本高、Emotion 明显停滞），新项目不建议 |

## 表单与数据校验

[React Hook Form](https://react-hook-form.com/)（7.x）+ [Zod 4](https://zod.dev/) 是最主流组合：非受控注册带来接近原生的输入性能，Zod 负责类型与校验一体化（v4 的 `z.compile()` 预编译让解析快约 9 倍）：

```tsx
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'

const schema = z.object({
  email: z.email(),              // Zod 4：格式校验提升为顶层 API
  password: z.string().min(8),
})
type FormData = z.infer<typeof schema>   // 校验规则即类型

function LoginForm() {
  const { register, handleSubmit, formState: { errors, isSubmitting } } = useForm<FormData>({
    resolver: zodResolver(schema),
  })
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}
      <button disabled={isSubmitting}>登录</button>
    </form>
  )
}
```

补充场景：TanStack Form v1（类型安全最激进，v2 alpha 已开跑）适合复杂表单；Conform 适合配合 Server Actions 的渐进增强表单；超简单表单直接用 React 19 的 `useActionState` 就够。

## 全栈框架与 RSC

### RSC：先理解再选框架

React Server Components（服务端组件）是理解 2026 年 React 全栈的钥匙：组件标记为仅在服务端运行，直接 async 取数、直连数据库，产物不进客户端 bundle；需要交互的部分拆成客户端组件（`'use client'`）。心智上类似"把 Nuxt 的 server routes 和组件合体"。

```tsx
// app/posts/page.tsx — 默认就是 Server Component
import { db } from '@/lib/db'

export default async function PostsPage() {
  const posts = await db.post.findMany()      // 直接查库，无需 API 层
  return (
    <ul>
      {posts.map(p => <li key={p.id}>{p.title}</li>)}
    </ul>
  )
}
```

### Next.js 16 要点

- Turbopack 成为所有新项目默认打包器（dev 与 build），16.3 起构建缓存持久化，官方将 16.3 标记为 Active LTS。
- `middleware.ts` 更名 `proxy.ts`；`params`/`searchParams`/`cookies`/`headers` 全部异步化（`await params`）。
- `cacheComponents`（原 Cache Components/dynamicIO）成为新的缓存模型入口。
- 16.3 带来 Instant Navigations（SPA 级即时导航）与 dev 内存大幅下降。

```tsx
// Next.js 16：params 是 Promise
export default async function UserPage({ params }: { params: Promise<{ id: string }> }) {
  const { id } = await params
  const user = await getUser(id)
  return <Profile user={user} />
}
```

### 其他框架现状

| 框架 | 状态 | 一句话 |
|------|------|--------|
| TanStack Start | v1 高频迭代 | Vite 底座 + Server Functions + 整页 SSR/流式，文档仍标 RC 但 API 已稳；RSC 以 1.x 增量落地 |
| React Router 8 框架模式 | 稳定 | Remix 的 loader/action 思想已并入 React Router；Remix 品牌转向独立的 Remix 3（RC） |
| Expo SDK 57 | 稳定 | React Native 官方推荐路线（RN 0.86），仅支持新架构；Expo Router 让 RN 与 Web 路由同构 |
| Waku | 1.0 RC | 轻量 RSC 元框架，适合营销站/headless 电商 |
| Rsbuild 2.2 | 稳定 | Rust 工具链替代 webpack，内置 Rust 版 React Compiler，Vite 之外的高性能选择 |

**选型结论**：产品站/SaaS 用 Next.js 16；纯前端中后台用 Vite + React Router 8；想要 Vite 心智的全栈用 TanStack Start；已有 webpack 存量工程迁移考虑 Rsbuild。

## 工程化与工具链

```bash
# 代码质量：ESLint 10（扁平配置已是默认）+ React 官方 hooks 插件 7.x（含 Compiler 规则）
npm install -D eslint eslint-plugin-react-hooks @eslint/js typescript-eslint
# 或一体化替代：Biome 2.x（Rust 实现，lint + format 一个工具）
npm install -D @biomejs/biome
```

2026 年工具链基线：TypeScript 6.0（最后一代 JS 实现）于 2026-03 发布，原生编译器的 [TypeScript 7.0](https://devblogs.microsoft.com/typescript/) 已于 2026-07 直接以 `typescript` 包成为 npm latest（大型项目类型检查提速一个量级）；包管理器默认 pnpm 12（2026-08 发布的 Rust 重写版；npm `latest` 标签暂仍指向 11 线，用 `pnpm self-update latest-12` 安装）；monorepo 用 pnpm workspace + Turborepo。

调试三件套：React DevTools（组件树/props/Profiler，Compiler 的 Memo 徽章也在这里看）、VS Code 的 ESLint 实时提示、Storybook 10（组件工作台 + 视觉回归）。

## 测试

Vitest 5 + React Testing Library 16 是组件测试标准，Playwright 做 E2E，MSW 2 拦截网络：

```tsx
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'

test('注册成功后显示欢迎语', async () => {
  render(<SignupPage />)
  await userEvent.type(screen.getByLabelText('邮箱'), 'kuma@example.com')
  await userEvent.click(screen.getByRole('button', { name: '注册' }))
  expect(await screen.findByText('注册成功')).toBeVisible()
})
```

测试哲学与 Vue Testing Library 一致：按用户视角（角色、文本）查询元素，不测实现细节。

## 动画、表格与可视化

- **Motion 13**（原 Framer Motion）：React 动画事实标准，13.x 新增基于 React 19.3 ViewTransition 的 `AnimateView`，路由级过渡更简单。
- `<ViewTransition>`：不引库就能做页面过渡，优先尝试。
- 表格：TanStack Table v9（headless，配合 shadcn/ui 的 DataTable 用法最流行）；虚拟滚动用 TanStack Virtual。
- 图表：Recharts（声明式）、ECharts（功能最全）、visx/nivo（深度定制）。

## Vue3 → React 速查表

| 我在 Vue 里写 | 在 React 里写 |
|----------------|----------------|
| `<script setup>` + 模板 | 函数组件 + JSX |
| `const count = ref(0)` | `const [count, setCount] = useState(0)` |
| `count.value++` | `setCount(c => c + 1)` |
| `const full = computed(() => ...)` | 渲染中直接算 / `useMemo`（Compiler 自动） |
| `watch(x, cb)` / `watchEffect` | `useEffect(cb, [x])` |
| `onMounted(() => ...)` | `useEffect(() => ..., [])` |
| `onUnmounted(() => ...)` | `useEffect` 返回的清理函数 |
| `provide` / `inject` | `createContext` + `useContext` |
| `defineProps` / `defineEmits` | `props` 参数 / 回调 prop |
| `v-model="text"` | `value={text} onChange={...}` |
| `v-if` / `v-else` | `{cond ? <A/> : <B/>}` |
| `v-show` | `style={{ display: cond ? '' : 'none' }}` 或 `<Activity>` |
| `v-for="item in items" :key` | `items.map(item => <Row key={item.id} />)` |
| `<slot>` / 具名插槽 | `children` / 以 prop 传 `ReactNode` |
| `ref` 模板引用 | `useRef` + `ref` prop |
| `<KeepAlive>` | `<Activity mode="hidden">` |
| `<Teleport>` | `createPortal` |
| `<Transition>` | `<ViewTransition>` / Motion |
| Pinia `defineStore` | Zustand `create` |
| Vue Router `useRoute` | `useParams` / `useSearchParams` / `useNavigate` |
| composable `useFoo()` | 自定义 Hook `useFoo()`（写法几乎一致） |
| `axios + ref` 手动管理请求 | TanStack Query 的 `useQuery` / `useMutation` |
| Element Plus / Naive UI | Ant Design / shadcn/ui / MUI |
| `npm create vue` | `npm create vite`（react-ts 模板）或 `create-next-app` |

## 常见坑

1. **直接改 state**：`obj.x = 1`、`arr.push()` 不触发更新，永远替换引用；深层嵌套用 immer。
2. **把 useEffect 当 watch 用**：在 effect 里 setState 存"派生数据"造成连环渲染，改为渲染中直接计算。
3. **列表 key 用下标**：插入/删除/排序时状态错位，用稳定业务 id。
4. **闭包陈旧值**：`setInterval(() => setCount(count + 1), 1e3)` 读到的是注册时的 count，改用函数式更新 `setCount(c => c + 1)`。
5. **受控/非受控混用**：`value` 从 undefined 变为有值会告警，初始给空字符串。
6. **StrictMode 双执行**：开发环境组件函数与 effect 各跑两次，是特性不是 bug，清理函数写对就没事。
7. **条件包裹 Hook**：Hook 不能放 if 里，`use()` 是唯一例外。
8. **忘开 Compiler 的旧习惯**：新项目别再满屏 useMemo/useCallback，交给编译器，只优化真正的长任务（transition）。
9. **`dangerouslySetInnerHTML`**：等价 `v-html`，未消毒的 HTML 是 XSS 直通车。
10. **照旧教程写 import**：React Router 8 已无 `react-router-dom`；TanStack Query v5 只收对象参数；`forwardRef` 已弃用——搜教程时注意版本。

## 2026 推荐技术栈组合

三套经过验证的组合，直接抄作业：

**A. 通用 SPA / 中后台（Vite 路线）**

```text
Vite 8 + React 19 + TypeScript 7
React Router 8        路由
Zustand 5             全局状态
TanStack Query 5      服务端状态
Tailwind CSS 4 + shadcn/ui（或 Ant Design 6.6）
React Hook Form + Zod 4
Vitest 5 + React Testing Library + Playwright
ESLint 10 + eslint-plugin-react-hooks 7 / Biome 2
```

**B. 全栈 SaaS / 产品站（Next.js 路线）**

```text
Next.js 16（Turbopack + App Router + Server Actions + React Compiler）
TanStack Query 5      客户端交互后的数据变更
Zod 4                 全栈校验共享
Prisma 或 Drizzle ORM + PostgreSQL
Tailwind CSS 4 + shadcn/ui
tRPC 11（可选，端到端类型安全 API）
```

**C. 跨端（React Native 路线）**

```text
Expo SDK 57 + React Native 0.86 + Expo Router
Zustand 5 + TanStack Query 5（与 Web 端逻辑层复用）
Tamagui 或 NativeWind（RN 版 Tailwind）
EAS Build / Update    云构建与热更新
```

## 官方资源与延伸阅读

| 资源 | 链接 |
|------|------|
| React Learn 教程主线（推荐按序读） | [react.dev/learn](https://react.dev/learn) |
| React 版本与博客 | [react.dev/versions](https://react.dev/versions) · [19.3 发布](https://react.dev/blog/2026/09/09/react-19-3) · [19.2 发布](https://react.dev/blog/2025/10/01/react-19-2) |
| React Compiler 安装指南 | [react.dev/learn/react-compiler/installation](https://react.dev/learn/react-compiler/installation) |
| Next.js 文档 | [nextjs.org/docs](https://nextjs.org/docs) |
| TanStack Query / Router / Start | [tanstack.com](https://tanstack.com/) |
| React Router 8 | [reactrouter.com](https://reactrouter.com/) |
| Vite 8 | [vite.dev](https://vite.dev/) |
| shadcn/ui | [ui.shadcn.com](https://ui.shadcn.com/) |
| Tailwind CSS 4 | [tailwindcss.com](https://tailwindcss.com/) |
| Zustand / Jotai | [zustand.docs.pmnd.rs](https://zustand.docs.pmnd.rs/) · [jotai.org](https://jotai.org/) |

进阶路线推荐：读熟 react.dev/learn 全部章节后，做一个小型全栈项目（Next.js 16 + Drizzle + shadcn/ui），再回头补 "Thinking in React" 与并发特性深水区（Suspense 边界设计、ViewTransition）。
