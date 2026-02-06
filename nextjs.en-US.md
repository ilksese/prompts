# AGENTS.md - Project Rules (Must Be Strictly Followed)

## Project Overview
This is a modern full-stack application built with Next.js App Router.  
- Next.js latest version (App Router architecture)  
- React 19 (prefer Server Components)  
- TypeScript (strict mode)  
- Database & Auth: Supabase (PostgreSQL + Auth + Storage)  
- UI: Tailwind CSS + daisyUI  
- Data fetching: TanStack Query (@tanstack/react-query v5+)  
- State management: Zustand (lightweight global / modular state)  
- Code quality: ESLint + Prettier (enforced)

## Core Architecture Principles
- **Server Components first**: All pages and components should be Server Components by default, unless client-side interactivity is required (useState, useEffect, browser APIs, etc.).  
- **"use client" directive**: Only add "use client" where absolutely necessary. Keep as much of the component tree as Server Components.  
- **Data fetching**: Use direct await fetch or Supabase client in Server Components; use TanStack Query in Client Components.  
- **Suspense + loading states**: Wrap all data fetching with <Suspense> and provide loading.tsx or fallback UI.  
- **React 19 features**: Prefer use() Hook for Promises, async Actions, startTransition, and optimistic updates.

## File Structure Guidelines
app/
├── layout.tsx              # Root layout (Server Component)
├── page.tsx                # Home page
├── (auth)/                 # Auth-related route groups (no URL prefix)
│   └── login/page.tsx
├── dashboard/              # Protected routes
│   ├── layout.tsx
│   └── page.tsx
components/
├── ui/                     # Reusable daisyUI / pure Tailwind components (Server or Client)
├── features/               # Business/feature-specific components, grouped by domain
lib/
├── supabase/               # Supabase clients (server & browser), types
├── queries/                # TanStack Query fetcher functions
├── stores/                 # Zustand stores (one file per store)
├── utils/                  # General utility functions
types/                      # Global TypeScript types
styles/                     # Global styles (globals.css, etc.)

## Coding Style & TypeScript
- TypeScript strict mode enabled (tsconfig: strict: true)  
- Use named exports (export function MyComponent)  
- Function components + Hooks only (no class components)  
- Prefer interface for component props (use type only when union/intersection is needed)  
- Add JSDoc comments to public components and utility functions  
- Naming: camelCase, clear and descriptive, avoid meaningless abbreviations  
- Prefer type inference; only add explicit types when necessary (complex generics, Supabase returns, etc.)

## Tailwind CSS + daisyUI Rules
- Prefer daisyUI class names (btn, card, navbar, drawer, etc.)  
- Custom styles go in className using Tailwind utility classes  
- No inline style={{}} — everything via Tailwind  
- Use daisyUI theme variables for colors (primary, secondary, accent, etc.)  
- Responsive design: use Tailwind prefixes (sm:, md:, lg:, etc.)  
- Dark mode: support data-theme="dark" and daisyUI theme switching  
- Encapsulate common daisyUI combinations into reusable components (place in components/ui/)

## Supabase Usage Rules
- Use @supabase/supabase-js createServerClient / createClient  
- Server Components / Server Actions → use server-only client  
- Client Components → use browser client (with anon key)  
- Generate and use Supabase types (supabase.gen.ts) for all tables/queries  
- Row Level Security (RLS) must be enabled; client operations must respect policies  
- Auth: Use Supabase Auth Helpers for Next.js (if integrated) or handle cookies/session manually

## TanStack Query Rules
- Use useQuery / useMutation  
- Query keys as arrays: ['todos', userId]  
- Server-side prefetch: QueryClient + dehydrate + HydrationBoundary  
- Error handling: unified via error boundary + toast notifications  
- Infinite scroll / pagination: useInfiniteQuery  
- Never fetch directly in useEffect — always go through Query

## Zustand Rules (Next.js specific)
- Do NOT create global singleton stores (causes SSR state leakage)  
- Create stores inside components or providers (recommended: createWithEqualityFn + shallow)  
- Simple global state (theme, user) → one shared store  
- Business / form / temporary UI state → prefer useState / useReducer  
- Use devtools middleware in development  
- SSR-safe: never access window / localStorage during store initialization

## Mandatory Rules
- No console.log / console.error in production code (remove or use proper logging)  
- Always handle loading / error / empty states for new features / changes  
- Forms: prefer React Hook Form + Zod (if added to project)  
- Commit messages: Conventional Commits (feat:, fix:, refactor:, etc.)  
- Code must pass ESLint + TypeScript checks before commit

## Security & Performance
- Never expose Supabase service_role key in Client Components  
- Use Server Actions for sensitive mutations  
- Images: always use Next.js <Image> component (auto-optimization)  
- Minimize "use client" usage to reduce client-side JS bundle  
- Prefer streaming + Suspense for better perceived performance

Always follow the rules above first. If the user request conflicts with these project rules, politely remind them of these enforced standards before proceeding.
