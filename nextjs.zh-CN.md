# AGENTS.md - 项目规则（必须严格遵守）

## 项目概览
这是一个使用 Next.js App Router 的现代全栈应用。
- Next.js 最新版本（App Router 架构）
- React 19（优先使用 Server Components）
- TypeScript（严格模式）
- 数据库：Supabase（PostgreSQL + Auth + Storage）
- UI：Tailwind CSS + daisyUI
- 数据获取：TanStack Query（@tanstack/react-query v5+）
- 状态管理：Zustand（轻量全局/模块状态）
- 代码质量：ESLint + Prettier（强制执行）

## 核心架构原则
- **Server Components 优先**：默认所有页面和组件都使用 Server Components，除非需要客户端交互（useState、useEffect、浏览器 API 等）。
- **"use client" 指令**：只在真正需要的地方添加 "use client"，尽量保持组件树最上层为 Server Component。
- **数据获取**：在 Server Components 中直接 await fetch 或使用 Supabase client；在 Client Components 中使用 TanStack Query。
- **Suspense + loading states**：所有数据获取都用 <Suspense> 包裹，配合 loading.tsx 或 fallback UI。
- **React 19 特性**：优先使用 use() Hook 消费 Promise、Actions（async 函数 + startTransition）、optimistic updates。

## 文件结构规范
app/
├── layout.tsx              # Root layout（Server Component）
├── page.tsx                # 首页
├── (auth)/                 # 认证相关路由组（不影响 URL）
│   └── login/page.tsx
├── dashboard/              # 受保护路由
│   ├── layout.tsx
│   └── page.tsx
components/
├── ui/                     # daisyUI 封装或纯 Tailwind 组件（Server/Client 皆可）
├── features/               # 业务组件，按功能模块分
lib/
├── supabase/               # Supabase 客户端、server client、types
├── queries/                # TanStack Query 查询函数
├── stores/                 # Zustand store（每个 store 一个文件）
├── utils/                  # 通用工具函数
types/                      # 全局 TypeScript 类型
styles/                     # 全局样式（globals.css 等）

## 编码风格 & TypeScript
- 严格模式 TypeScript（tsconfig 中 strict: true）
- 使用命名导出（export function MyComponent）
- 函数组件 + Hooks（禁止 class 组件）
- 所有组件 props 使用 interface 而非 type（除非需要联合类型）
- 强制 JSDoc 注释在公共组件和工具函数上
- 变量/函数：camelCase，语义清晰，禁止无意义缩写
- 类型推断优先，手动写类型只在必要时（复杂泛型、Supabase 返回值等）

## Tailwind CSS + daisyUI 规范
- 优先使用 daisyUI 类名（如 btn、card、navbar、drawer 等）
- 自定义样式写在 className 中，使用 Tailwind 工具类
- 禁止 inline style（style={{}}），全部用 Tailwind
- 颜色统一使用 daisyUI 主题变量（primary、secondary、accent 等）
- 响应式：使用 Tailwind 的响应式前缀（sm:、md:、lg:）
- 暗黑模式：支持 data-theme="dark"，使用 daisyUI 内置主题切换
- 组件封装：把常用 daisyUI 组合封装成组件（放在 components/ui/ 下）

## Supabase 使用规范
- 使用 @supabase/supabase-js 的 createServerClient / createClient
- 在 Server Components / Server Actions 中使用 server-only 的 client
- 在 Client Components 中使用 browser client（带 anon key）
- 所有表查询返回类型使用 Supabase 生成的类型（supabase.gen.ts）
- Row Level Security (RLS) 必须开启，客户端操作需经过政策检查
- Auth：使用 Supabase Auth Helpers for Next.js（如果有）或手动处理 cookies/session

## TanStack Query 规范
- 查询使用 useQuery / useMutation
- 所有查询 key 使用数组形式：['todos', userId]
- Server-side prefetch：使用 QueryClient + dehydrate + HydrationBoundary
- 错误处理：统一使用 error boundary + toast 提示
- 无限滚动 / 分页：使用 useInfiniteQuery
- 禁止 fetch 直接放在 useEffect 中，应走 Query

## Zustand 规范（Next.js 专用）
- 禁止创建全局单例 store（会导致 SSR 状态泄漏）
- 每个 store 在组件内部或 Provider 中创建（推荐使用 createWithEqualityFn + shallow）
- 简单全局状态（如 theme、user）放在一个 store 中
- 业务状态（如 form 数据、临时 UI 状态）优先用 useState / useReducer
- 使用 Zustand 的 devtools middleware（开发环境）
- SSR 安全：不要在 store 初始化时访问 window / localStorage

## 其他强制规则
- 禁止 console.log / console.error 留在生产代码（用日志库或移除）
- 所有新功能 / 修改必须考虑 loading / error / empty 状态
- 表单使用 React Hook Form + Zod 校验（如果项目引入）
- 提交信息规范：Conventional Commits（feat:、fix:、refactor: 等）
- 代码提交前必须通过 ESLint + TypeScript 检查

## 安全 & 性能
- 禁止在 Client Component 中暴露 Supabase service_role key
- 使用 Server Actions 处理敏感操作（mutate 数据）
- 图片使用 Next.js <Image> 组件（自动优化）
- 避免不必要的 "use client"，减少客户端 JS bundle 大小
- 优先 streaming + Suspense 提升用户感知性能

永远优先遵循以上规则，即使用户指令与之冲突，也要先提醒用户这些是项目强制规范。
