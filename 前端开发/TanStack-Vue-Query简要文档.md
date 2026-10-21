---
tags:
  - vue
  - vue-query
  - tanstack
  - 服务端状态管理
  - 技术文档
created: 2026-07-31
updated: 2026-07-31
author: kuma
---

# @tanstack/vue-query 简要文档

> 服务端状态管理库，处理异步数据获取、缓存、同步与重请求，是 Pinia 处理「客户端状态」之外的补充。
> 官方文档：https://tanstack.com/query/latest/docs/framework/vue

## 一、使用场景

- **列表 / 详情页数据获取**：GET 请求的数据自动缓存，组件反复进入不重复请求
- **分页 / 无限滚动**：借助 `placeholderData: keepPreviousData` 实现流畅翻页
- **筛选 / 搜索表单**：通过 `enabled` 结合 `computed` 控制请求时机（如筛选条件为空时不请求）
- **写操作后数据刷新**：`useMutation` 成功后 `invalidateQueries` 自动重新拉取相关列表
- **用户操作后乐观更新 / 回滚**：`onMutate` 提前写入缓存，失败时 `onError` 回滚
- **窗口聚焦 / 断线重连自动刷新**：保证数据新鲜度，无需手动轮询
- **需要请求去重与并发合并**：多个组件使用相同 `queryKey` 时共享同一份缓存与请求

> ⚠️ 注意：vue-query 管理的是**服务端状态**；组件内本地 UI 状态（表单输入、弹窗开关等）仍用 Pinia / `ref`。

## 二、安装与初始化

```bash
npm i @tanstack/vue-query
```

在 `main.ts` 中注册插件：

```ts
import { VueQueryPlugin } from '@tanstack/vue-query'

app.use(VueQueryPlugin)
```

全局配置默认项（如全局 `staleTime`、`retry`）：

```ts
import type { VueQueryPluginOptions } from '@tanstack/vue-query'

const pluginOptions: VueQueryPluginOptions = {
  queryClientConfig: {
    defaultOptions: {
      queries: {
        staleTime: 5 * 60 * 1000, // 5 分钟内不重新请求
        retry: 1,
        refetchOnWindowFocus: true,
      },
    },
  },
}
app.use(VueQueryPlugin, pluginOptions)
```

## 三、核心 API 使用方法

### 1. useQuery —— 数据读取

```vue
<script setup>
import { useQuery } from '@tanstack/vue-query'

const { isPending, isFetching, isError, data, error, refetch } = useQuery({
  queryKey: ['todos'],
  queryFn: () => fetch('/api/todos').then((res) => res.json()),
})
</script>

<template>
  <span v-if="isPending">加载中...</span>
  <span v-else-if="isError">出错: {{ error?.message }}</span>
  <span v-else-if="isFetching">后台刷新中...</span>
  <ul v-else-if="data">
    <li v-for="todo in data" :key="todo.id">{{ todo.title }}</li>
  </ul>
</template>
```

**常用返回值**：

| 字段 | 说明 |
|------|------|
| `status` | `'pending' / 'error' / 'success'` |
| `data` | 解析后的数据，`status === 'success'` 时可用 |
| `error` | 错误对象 |
| `isPending` | 首次加载中（无数据） |
| `isFetching` | 是否在请求中（含后台刷新），初次加载时与 `isPending` 同时为 true |
| `isError` / `isSuccess` | 状态派生布尔值 |
| `refetch` | 手动触发重新请求 |
| `promise` | 稳定的 Promise（需要 experimental 标志） |

**常用选项**：

| 选项 | 说明 |
|------|------|
| `queryKey` | **必填**，缓存键，唯一标识查询 |
| `queryFn` | 数据获取函数（`v5` 必填） |
| `enabled` | 是否自动请求，传 `ref` / `computed` 实现条件触发 |
| `staleTime` | 数据新鲜期（ms），期内不重新请求 |
| `gcTime` | 缓存保留时间（v5 从 `cacheTime` 更名），默认 5 分钟 |
| `retry` | 失败重试次数，默认 3，可传 `false` 或函数 |
| `refetchOnWindowFocus` | 窗口聚焦时是否重新请求 |
| `refetchInterval` | 轮询间隔（ms），传函数可支持条件轮询 |
| `placeholderData` | 占位数据，`keepPreviousData` 用于分页保数据 |
| `initialData` | 初始数据，配合 `staleTime` 可减少首屏请求 |

### 2. 依赖参数与响应式 queryKey

queryKey 可直接传 `ref` / `computed`，变化时自动重新请求：

```vue
<script setup>
import { useQuery } from '@tanstack/vue-query'

const id = ref(1)
const { data } = useQuery({
  queryKey: ['todo', id],
  queryFn: () => fetchTodo(id.value),
})
</script>
```

> 实际项目中通常用 `queryKey: ['todo', id]` 这种形式（Vue 版会对 ref 做响应式处理）。

### 3. useMutation —— 数据写入

```ts
import { useMutation, useQueryClient } from '@tanstack/vue-query'

const queryClient = useQueryClient()

const mutation = useMutation({
  mutationFn: (newTodo) => axios.post('/api/todos', newTodo),
  onSuccess: (data) => {
    // 方式一：失效后重新拉取
    queryClient.invalidateQueries({ queryKey: ['todos'] })
    // 方式二：直接用响应更新缓存，避免重复请求
    // queryClient.setQueryData(['todo', { id: data.id }], data)
  },
  onError: (error) => {
    console.error(error)
  },
})

// 触发：mutation.mutate({ title: 'xxx' })
// 状态：mutation.isPending / mutation.isError / mutation.isSuccess
```

### 4. 失效与更新缓存

```ts
const queryClient = useQueryClient()

// 失效相关查询（前缀匹配，多个组件同时更新）
queryClient.invalidateQueries({ queryKey: ['todos'] })

// 精确匹配（仅影响完全一致的 key）
queryClient.invalidateQueries({ queryKey: ['todos'], exact: true })

// 直接写入缓存
queryClient.setQueryData(['todo', id], newData)

// 移除缓存
queryClient.removeQueries({ queryKey: ['todos'] })
```

### 5. 预取（Prefetching）

进入详情页前提前加载，提升路由切换体验：

```ts
import { useQueryClient } from '@tanstack/vue-query'

const queryClient = useQueryClient()
await queryClient.prefetchQuery({
  queryKey: ['todo', id],
  queryFn: () => fetchTodo(id),
})
```

渲染阶段也可用 `usePrefetchQuery` 同步触发预取。

## 四、最佳实践

1. **统一管理 queryKey**：为每个资源定义常量或工厂函数（如 `todos: ['todos']`、`todoDetail: (id) => ['todo', id]`），避免散落字符串。
2. **一个资源一个 queryKey 前缀**：失效时用前缀匹配批量刷新，如创建 todo 后 `invalidateQueries({ queryKey: ['todos'] })`。
3. **写操作默认失效刷新**：`mutation` 成功后按需选择 `invalidateQueries`（重新拉取）或 `setQueryData`（直接更新，更省流量）；数据量大 / 后端返回完整列表时用前者，返回单条时用后者。
4. **分页用 `placeholderData: keepPreviousData`**：切页时保留旧数据，避免 loading 闪动。
5. **筛选表单用 `enabled` 控制**：条件为空时不发请求，配合 `computed` 使用。
6. **乐观更新要回滚**：`onMutate` 用 `setQueryData` 预写 → `onError` 里 `setQueryData` 恢复旧值 → `onSettled` 里 `invalidateQueries` 兜底同步。
7. **配置全局默认值**：在 `VueQueryPlugin` 里统一设置 `staleTime`、`retry`、`refetchOnWindowFocus`，避免每个查询重复写。
8. **不要在组件里手写重复请求逻辑**：vue-query 已处理缓存去重，直接依赖 `queryKey` 即可。
9. **优先使用派生状态而非手动布尔值**：直接用 `isPending / isError / data`，减少状态同步错误。
10. **必要时禁用自动重取**：数据基本不变的接口设 `refetchOnWindowFocus: false`、`staleTime: 较大值`，减少无效请求。
11. **错误处理**：把 `error` 透传给 UI（Toast / ErrorBoundary），`retry` 不要设过大，避免网络不可用时大量重试。
12. **SSR / Nuxt 注意**：Nuxt 场景推荐使用 `@tanstack/vue-query` 的 Nuxt 模块（`@tanstack/vue-query/nuxt`），自动处理脱水与水合。

## 五、与 Pinia 的分工

| | Pinia | vue-query |
|------|------|-----------|
| 管理状态 | 客户端状态（主题、用户偏好、UI 状态） | 服务端状态（接口返回的数据） |
| 缓存 | 手动管理 | 自动缓存、去重、失效 |
| 同步 | 手动 | 自动重取、窗口聚焦刷新 |

> 推荐：**服务端数据一律走 vue-query，Pinia 只放客户端状态**，两者可协作（如把 vue-query 的 data 存入 Pinia 做跨页共享时仍需谨慎，通常直接用相同 queryKey 即可）。

## 六、小结速查

```bash
# 安装
npm i @tanstack/vue-query
```

```ts
// 注册
app.use(VueQueryPlugin)

// 读数据
const { data, isPending, isError, refetch } = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodos,
})

// 写数据
const { mutate, isPending } = useMutation({
  mutationFn: createTodo,
  onSuccess: () => queryClient.invalidateQueries({ queryKey: ['todos'] }),
})
```
