# reactjs-skill

[![npm version](https://img.shields.io/npm/v/reactjs-skill.svg?color=blue)](https://www.npmjs.com/package/reactjs-skill)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![React](https://img.shields.io/badge/React-18%20%7C%2019-blue.svg)](https://react.dev)
[![Next.js](https://img.shields.io/badge/Next.js-App%20Router-black.svg)](https://nextjs.org)
[![Redux Toolkit](https://img.shields.io/badge/Redux%20Toolkit-RTK-purple.svg)](https://redux-toolkit.js.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-Strict-blue.svg)](https://www.typescriptlang.org)

> An open-source, enterprise-grade AI Agent Skill and architectural reference guide for building, structuring, and maintaining production-ready React applications across **all React frameworks** (Vite SPA, Next.js App/Pages Router, Remix / React Router v7, React Native / Expo, Astro React, etc.).

---

## 📑 Table of Contents

- [📦 Quick Start & Installation](#-quick-start--installation)
- [🧭 What is `reactjs-skill`?](#-what-is-reactjs-skill)
- [🔒 Strict Rules vs. Flexible Guidelines](#-strict-rules-vs-flexible-guidelines)
- [🏗️ Architecture Selection Matrix](#️-architecture-selection-matrix)
- [📖 Core Learning Modules](#-core-learning-modules)
  - [1. Feature-Driven Architecture (Default)](#1-feature-driven-architecture-default)
  - [2. Next.js App Router Architecture (RSC & Actions)](#2-nextjs-app-router-architecture-rsc--actions)
  - [3. Type Safety & Memory Leak Prevention](#3-type-safety--memory-leak-prevention)
  - [4. React Redux (Redux Toolkit / RTK) Structure](#4-react-redux-redux-toolkit--rtk-structure)
  - [5. Data Fetching, Pagination & Skeleton Loaders](#5-data-fetching-pagination--skeleton-loaders)
  - [6. Version-Aware Tailwind CSS & Enterprise Clean UI](#6-version-aware-tailwind-css--enterprise-clean-ui)
  - [7. Code Chunking into Custom Hooks](#7-code-chunking-into-custom-hooks)
  - [8. Form Validation & Production UI Micro-Details](#8-form-validation--production-ui-micro-details)
- [📂 Repository Directory Structure](#-repository-directory-structure)
- [🤖 AI Agent Invocation & Usage](#-ai-agent-invocation--usage)
- [🔗 Reference Guides Index](#-reference-guides-index)
- [📄 License & Author Info](#-license--author-info)

---

## 📦 Quick Start & Installation

### Option 1: Use with `npx-skills` / Agent Skills Ecosystem
```bash
# Add reactjs-skill to your local workspace or agent environment
npx skills add reactjs-skill
```

### Option 2: Clone / Install into Antigravity IDE
Place `reactjs-skill` inside your local workspace or `.agents/skills` / `.gemini/skills` directory:
```bash
git clone https://github.com/ac-aman/reactjs-skill.git .agents/skills/reactjs-skill
```

---

## 🧭 What is `reactjs-skill`?

`reactjs-skill` is an unopinionated yet strict reference and orchestration engine designed for **both human engineers and AI coding assistants** (Antigravity IDE, Claude, Cursor, Copilot).

---

## 🔒 Strict Rules vs. Flexible Guidelines

### 🛑 MANDATORY STRICT REQUIREMENTS (Non-Negotiable Production Necessities)
1. **Security Standards**:
   - **XSS Sanitization**: `DOMPurify` required for raw HTML.
   - **Token Privacy**: `HttpOnly` cookies for auth session tokens (never store sensitive JWTs in `localStorage`).
   - **Mutation Validation**: Zod schema validation for all client data mutations.
2. **Form Validation & Formal UI Necessities**:
   - Explicit `<label htmlFor="...">` for every input field.
   - Accessible ARIA helper text and error alerts (`aria-describedby`, `aria-invalid`).
   - Double-submit prevention (disabling async buttons and showing loading spinners during mutations).
   - Action-driven micro-copy ("Save Profile" vs "Submit").
3. **Memory Leak Prevention & Cleanups**:
   - Aborting unmounted network requests (`AbortController.abort()`) and disposing event listeners/intervals.

### 🎨 FLEXIBLE ADAPTIVE GUIDELINES (Project Preferences)
- **Folder Architecture**: Feature-Driven, Layered, App Router, Clean, or Atomic Design are **flexible recommendations**. Adapt to whatever the project already uses without forcing unwanted refactoring.
- **State Management**: Redux Toolkit, Zustand, TanStack Query, React Context, or Jotai are **flexible options**. Follow whatever state tool is installed or requested.
- **Styling Options**: Tailwind CSS (v3 or v4), CSS Modules, Styled Components, Shadcn UI, or Material UI are **adaptable tools**. Follow package versions installed in `package.json`.

---

## 🏗️ Architecture Selection Matrix

`reactjs-skill` includes 5 complete architecture reference specifications:

| Architecture Pattern | Primary Use Case | Key Characteristic | Reference Guide |
| :--- | :--- | :--- | :--- |
| **Feature-Driven (Vertical Slice)** *(Default)* | Medium-to-large production web apps | Grouped by business domain (`features/auth`, `features/checkout`) with public `index.ts` API barriers | [`references/architectures/feature-driven.md`](./skills/reactjs-skill/references/architectures/feature-driven.md) |
| **Next.js App Router** | Next.js 13+ / 14 / 15 web apps | React Server Components (RSC), Client Leaf components, Route Groups, Server Actions | [`references/architectures/nextjs-app-router.md`](./skills/reactjs-skill/references/architectures/nextjs-app-router.md) |
| **Layered (Horizontal)** | Small SPAs, prototypes, single-purpose apps | Separation by file type (`components/`, `hooks/`, `services/`, `store/`) | [`references/architectures/layered.md`](./skills/reactjs-skill/references/architectures/layered.md) |
| **Clean Architecture (Hexagonal)** | Complex enterprise apps & heavy client logic | Decouples pure domain entities and use cases from React UI frameworks and network clients | [`references/architectures/clean-architecture.md`](./skills/reactjs-skill/references/architectures/clean-architecture.md) |
| **Atomic Design** | Design systems & UI component libraries | Hierarchy: Atoms → Molecules → Organisms → Templates → Pages | [`references/architectures/atomic-design.md`](./skills/reactjs-skill/references/architectures/atomic-design.md) |

---

## 📖 Core Learning Modules

### 1. Feature-Driven Architecture (Default)

Organizes code around **business capabilities** instead of technical layer types.

```text
src/
├── features/
│   ├── auth/
│   │   ├── api/            # TanStack Query hooks & HTTP requests
│   │   ├── components/     # Feature-specific UI components
│   │   ├── hooks/          # Custom hooks (e.g. useAuth)
│   │   ├── store/          # Feature state slice
│   │   ├── types/          # Domain TypeScript contracts
│   │   └── index.ts        # Public API barrier (exports public symbols only)
│   └── checkout/
├── shared/                 # Generic UI primitives (Button, Modal, Input)
└── app/                    # Providers, router, global CSS
```

---

### 2. Next.js App Router Architecture (RSC & Actions)

Enforces **Server Components First** with narrow Client Component leaf nodes and type-safe Server Actions.

```tsx
// app/dashboard/page.tsx (Server Component - Zero client JS bundle overhead!)
import { Suspense } from 'react';
import { getAnalyticsData } from '@/features/analytics/queries';
import { AnalyticsChart } from '@/features/analytics/components/AnalyticsChart';
import { ChartSkeleton } from '@/features/analytics/components/ChartSkeleton';

export default async function DashboardPage() {
  const data = await getAnalyticsData();

  return (
    <main className="p-6 space-y-6">
      <h1 className="text-2xl font-bold">Analytics Dashboard</h1>
      <Suspense fallback={<ChartSkeleton />}>
        <AnalyticsChart initialData={data} />
      </Suspense>
    </main>
  );
}
```

---

### 3. Type Safety & Memory Leak Prevention

Guarantees **zero `any` types** and mandates cleanup functions to prevent memory leaks and unmounted component state updates.

```tsx
import { useEffect, useState } from 'react';

export function useUserProfile(userId: string) {
  const [user, setUser] = useState<User | null>(null);

  useEffect(() => {
    const controller = new AbortController();

    async function fetchUser() {
      try {
        const res = await fetch(`/api/users/${userId}`, { signal: controller.signal });
        const data = await res.json();
        setUser(data);
      } catch (err: unknown) {
        if (err instanceof Error && err.name !== 'AbortError') {
          console.error('Fetch error:', err.message);
        }
      }
    }

    fetchUser();

    return () => controller.abort();
  }, [userId]);

  return user;
}
```

---

### 4. React Redux (Redux Toolkit / RTK) Structure

When React Redux is used, follow the official Redux Toolkit (RTK) standard structure:

```text
src/
├── app/
│   ├── store.ts            # Centralized configureStore
│   └── hooks.ts            # Typed hooks: useAppDispatch & useAppSelector
└── features/
    └── counter/
        ├── counterSlice.ts # createSlice (reducers & actions)
        └── Counter.tsx     # Component consuming Redux
```

---

### 5. Data Fetching, Pagination & Skeleton Loaders

Keep pagination state in URL search parameters so pages are bookmarkable and shareable. Render Skeleton loaders to eliminate layout shifts (CLS).

```tsx
import { useSearchParams } from 'react-router-dom';
import { keepPreviousData, useQuery } from '@tanstack/react-query';

export function PaginatedUserList() {
  const [searchParams, setSearchParams] = useSearchParams();
  const page = Number(searchParams.get('page')) || 1;

  const { data, isLoading, isFetching } = useQuery({
    queryKey: ['users', page],
    queryFn: () => fetchUsers({ page, limit: 10 }),
    placeholderData: keepPreviousData,
  });

  if (isLoading) return <UserListSkeleton />;

  return (
    <div className="space-y-4">
      <div className={isFetching ? 'opacity-60' : 'opacity-100'}>
        <UserTable users={data?.users ?? []} />
      </div>
      <button onClick={() => setSearchParams({ page: String(page + 1) })}>Next Page</button>
    </div>
  );
}
```

---

### 6. Version-Aware Tailwind CSS & Enterprise Clean UI

Inspect `package.json` to verify whether the project uses Tailwind v3 or Tailwind v4. Use theme tokens (`text-primary`, `bg-card`, `border-border`) rather than hardcoding arbitrary hex values.

```tsx
import { Button } from '@/components/ui/button';
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card';

export function CleanUserProfileCard({ user }: { user: User }) {
  return (
    <Card className="border-border bg-card text-card-foreground">
      <CardHeader>
        <CardTitle className="text-lg font-semibold">{user.name}</CardTitle>
      </CardHeader>
      <CardContent className="space-y-3">
        <p className="text-sm text-muted-foreground">{user.email}</p>
        <Button variant="default" size="sm">
          View Details
        </Button>
      </CardContent>
    </Card>
  );
}
```

---

### 7. Code Chunking into Custom Hooks

Keep components small and presentational (~100 lines max). Extract state, validation, and side-effects into custom hooks.

```text
❌ Monolithic 200-line Component (state + fetching + handlers + layout)
                    │
                    ▼
✅ Clean Chunked Architecture:
  ├── Custom Hook (`useUserFilter.ts` - handles queries & filter state)
  └── Presentational Component (`UserFilterTable.tsx` - clean readable JSX)
```

---

### 8. Form Validation & Production UI Micro-Details

Enforce Zod schema validation, explicit input labels with `aria-describedby`, action-driven button copy ("Create Workspace" vs "Submit"), and double-submit prevention.

```tsx
<div className="space-y-1.5">
  <label htmlFor="email" className="block text-sm font-medium">
    Work Email <span className="text-destructive">*</span>
  </label>
  <input
    id="email"
    type="email"
    inputMode="email"
    autoComplete="email"
    aria-invalid={!!errors.email}
    aria-describedby={errors.email ? 'email-error' : undefined}
    {...register('email')}
  />
  {errors.email && (
    <p id="email-error" className="text-xs text-destructive font-medium" role="alert">
      {errors.email.message}
    </p>
  )}
</div>
```

---

## 📂 Repository Directory Structure

```text
reactjs-skill/
├── package.json                                 # Package manifest
├── README.md                                     # NPM-style documentation & guide
└── skills/
    └── reactjs-skill/
        ├── SKILL.md                             # AI Agent execution manifest & workflow
        └── references/                          # Detailed technical documentation
            ├── architectures/                   # 5 Architecture Specifications
            │   ├── feature-driven.md            # Vertical Slice Architecture (Default)
            │   ├── nextjs-app-router.md         # Next.js 13+ RSC & Server Actions
            │   ├── layered.md                   # Horizontal Layered Architecture
            │   ├── atomic-design.md             # Design Systems & Component Libraries
            │   └── clean-architecture.md        # Hexagonal / DDD Architecture
            │
            └── guidelines/                      # 11 Professional Guidelines
                ├── forms-and-ui-microdetails.md # Zod Validation, Labels & Button Copy [STRICT]
                ├── security-and-a11y.md         # XSS, Auth Cookies & WCAG a11y [STRICT]
                ├── typescript.md                # Strict TS & Memory Leak Prevention [STRICT]
                ├── state-management.md          # State Strategy & Redux Toolkit (RTK)
                ├── data-fetching.md             # Async Fetching, Pagination & Loaders
                ├── styling-and-ui.md            # Tailwind v3/v4 & Enterprise Clean UI
                ├── folder-structure.md          # Folder layouts & file naming
                ├── component-design.md          # Composition & Code Chunking
                ├── performance.md               # Re-render prevention & virtualization
                ├── testing.md                   # Vitest, React Testing Library, Playwright
                └── official-docs-and-resources.md # Direct links to official docs
```

---

## 🤖 AI Agent Invocation & Usage

When using Antigravity IDE or compatible agent tools, `reactjs-skill` activates automatically when you prompt for React architectural guidance or development tasks.

### Manual Example Prompts:
> **Prompt 1**: "Use `reactjs-skill` to create a Feature-Driven architecture layout for an e-commerce dashboard with Vite and Zustand."
>
> **Prompt 2**: "Refactor `UserProfile.tsx` following `reactjs-skill` guidelines: chunk the logic into custom hooks, enforce TypeScript type safety with AbortController cleanup, and convert hardcoded colors to Tailwind theme tokens."
>
> **Prompt 3**: "Set up Redux Toolkit for our authentication feature using the `reactjs-skill` Redux guidelines (`configureStore`, `createSlice`, and typed hooks)."

---

## 🔗 Reference Guides Index

- 🛡️ [Security & Accessibility (a11y) Standards [STRICT]](./skills/reactjs-skill/references/guidelines/security-and-a11y.md)
- 📋 [Form Validation & Production UI Micro-Details [STRICT]](./skills/reactjs-skill/references/guidelines/forms-and-ui-microdetails.md)
- 🟦 [TypeScript Standards & Memory Leak Prevention [STRICT]](./skills/reactjs-skill/references/guidelines/typescript.md)
- 🧠 [State Management & React Redux (RTK)](./skills/reactjs-skill/references/guidelines/state-management.md)
- 🌐 [Data Fetching, Pagination & Loaders](./skills/reactjs-skill/references/guidelines/data-fetching.md)
- 🎨 [Styling, Tailwind CSS & Enterprise UI](./skills/reactjs-skill/references/guidelines/styling-and-ui.md)
- 📂 [Folder Structure & File Naming](./skills/reactjs-skill/references/guidelines/folder-structure.md)
- 🧩 [Component Design & Code Chunking](./skills/reactjs-skill/references/guidelines/component-design.md)
- ⚡ [Performance & Virtualization](./skills/reactjs-skill/references/guidelines/performance.md)
- 🧪 [Testing Strategy (Vitest, RTL, Playwright)](./skills/reactjs-skill/references/guidelines/testing.md)
- 🔗 [Official Documentation Links](./skills/reactjs-skill/references/guidelines/official-docs-and-resources.md)

---

## 📄 License & Author Info

MIT © [ac-aman](https://github.com/ac-aman)
