# reactjs-skill

[![npm version](https://img.shields.io/npm/v/reactjs-skill.svg?color=blue)](https://www.npmjs.com/package/reactjs-skill)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![React](https://img.shields.io/badge/React-18%20%7C%2019-blue.svg)](https://react.dev)
[![Next.js](https://img.shields.io/badge/Next.js-App%20Router-black.svg)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-Strict-blue.svg)](https://www.typescriptlang.org)

An enterprise-grade **AI Agent Skill** and production reference guide for building, structuring, and maintaining React applications across all frameworks (Vite, Next.js, Remix, React Native / Expo, Astro, etc.).

---

## ⚡ Quick Start & Installation

### Option 1: Install with `npx skills` (Recommended)

```bash
npx skills add ac-aman/reactjs-skill
```

### Option 2: Install via npm
```bash
npm i reactjs-skill
```

### Option 3: Manual Clone for Antigravity IDE / Cursor / Claude
Clone directly into your workspace's `.agents/skills` or `.gemini/skills` directory:

```bash
git clone https://github.com/ac-aman/reactjs-skill.git .agents/skills/reactjs-skill
```

---

## 🔒 Core Principles: Strict Rules vs. Flexible Adaptability

- **🛑 MANDATORY STRICT RULES**:
  - **Security & Privacy**: XSS sanitization (`DOMPurify`), `HttpOnly` cookie auth tokens (no JWTs in `localStorage`), Zod input validation.
  - **Accessibility & Forms**: Explicit `<label htmlFor="...">`, ARIA descriptors (`aria-describedby`, `aria-invalid`), double-submit protection with loading spinners, action-driven copy.
  - **Memory Leak Protection**: Network request cancellation (`AbortController`) and cleanup functions on unmount.
  - **Type Safety**: No explicit `any` types; strict interface definitions.

- **🎨 FLEXIBLE ADAPTABILITY**:
  - Unopinionated regarding folder architecture, state management (Redux Toolkit, Zustand, TanStack Query), or UI framework (Tailwind v3/v4, CSS Modules, Shadcn UI). Adapt seamlessly to existing codebases.

---

## 🏗️ Architecture Selection Matrix

| Architecture Pattern | Best For | Key Characteristic | Reference Guide |
| :--- | :--- | :--- | :--- |
| **Feature-Driven** *(Default)* | Medium-to-large production apps | Vertical slices (`features/auth`, `features/checkout`) with public `index.ts` barrier | [`feature-driven.md`](./skills/reactjs-skill/references/architectures/feature-driven.md) |
| **Next.js App Router** | Next.js 13+ / 14 / 15 | RSC-first, Client Leaf nodes, Route Groups, Server Actions | [`nextjs-app-router.md`](./skills/reactjs-skill/references/architectures/nextjs-app-router.md) |
| **Layered (Horizontal)** | Small SPAs & prototypes | Technical separation (`components/`, `hooks/`, `services/`) | [`layered.md`](./skills/reactjs-skill/references/architectures/layered.md) |
| **Clean Architecture** | Enterprise apps with rich client logic | Decoupled domain entities and use-cases from UI layer | [`clean-architecture.md`](./skills/reactjs-skill/references/architectures/clean-architecture.md) |
| **Atomic Design** | Design systems & UI libraries | Atoms → Molecules → Organisms → Templates → Pages | [`atomic-design.md`](./skills/reactjs-skill/references/architectures/atomic-design.md) |

---

## 📚 Technical Guidelines Index

- 🛡️ **[Security & Accessibility (a11y)](./skills/reactjs-skill/references/guidelines/security-and-a11y.md)** — XSS prevention, Auth cookies & WCAG standards `[STRICT]`
- 📋 **[Forms & UI Micro-Details](./skills/reactjs-skill/references/guidelines/forms-and-ui-microdetails.md)** — Zod validation, accessible labels & double-submit handling `[STRICT]`
- 🟦 **[TypeScript & Memory Leaks](./skills/reactjs-skill/references/guidelines/typescript.md)** — Strict typing & AbortController cleanups `[STRICT]`
- 🧠 **[State Management & Redux (RTK)](./skills/reactjs-skill/references/guidelines/state-management.md)** — RTK slices, Zustand & TanStack Query patterns
- 🌐 **[Data Fetching & Pagination](./skills/reactjs-skill/references/guidelines/data-fetching.md)** — URL search params state, caching & Skeleton loaders
- 🎨 **[Styling & UI Systems](./skills/reactjs-skill/references/guidelines/styling-and-ui.md)** — Tailwind v3/v4 theme tokens & clean component styling
- 📂 **[Folder Structure & Naming](./skills/reactjs-skill/references/guidelines/folder-structure.md)** — File naming conventions & directory layouts
- 🧩 **[Component Design & Chunking](./skills/reactjs-skill/references/guidelines/component-design.md)** — Small presentational components & custom hook extraction
- ⚡ **[Performance & Virtualization](./skills/reactjs-skill/references/guidelines/performance.md)** — Re-render prevention, memoization & virtual lists
- 🧪 **[Testing Strategy](./skills/reactjs-skill/references/guidelines/testing.md)** — Vitest, React Testing Library & Playwright integration
- 🔗 **[Official Docs Reference](./skills/reactjs-skill/references/guidelines/official-docs-and-resources.md)** — Direct links to React, Next.js, Redux & Tailwind docs

---

## 🤖 AI Agent Invocation Examples

Prompt your AI coding assistant (Antigravity IDE, Claude, Cursor, Copilot) with tasks like:

```text
"Use reactjs-skill to create a Feature-Driven layout for an e-commerce dashboard with Vite and Zustand."
```
```text
"Refactor UserProfile.tsx according to reactjs-skill guidelines: chunk logic into custom hooks, enforce strict TypeScript with AbortController cleanup, and add double-submit protection."
```
```text
"Set up Redux Toolkit for authentication following reactjs-skill RTK standards (configureStore, createSlice, typed hooks)."
```

---

## 📄 License & Author

MIT © [ac-aman](https://github.com/ac-aman)
