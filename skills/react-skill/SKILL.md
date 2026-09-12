---
name: react-skill
description: Execution guide for building, architecting, and refactoring React applications across all frameworks (Vite, Next.js, Remix, React Native) using scalable folder structures, clean component design, version-aware UI libraries, state management (Redux Toolkit, Zustand), form validation, and evidence-based implementation.
license: MIT
metadata:
  author: "ac-aman"
  version: "1.0.4"
  tags:
    - react
    - reactjs
    - nextjs
    - vite
    - redux
    - typescript
    - frontend
    - component-architecture
    - feature-driven
    - tailwindcss
    - validation
    - security
    - workflow
---

# React Development & Architecture Guide

Use this skill when creating, extending, or refactoring React applications across any framework (Vite SPA, Next.js App/Pages Router, Remix, React Router v7, React Native, Astro, etc.).

This document serves as the **execution, orchestration, and reference loading guide** for AI coding agents and human engineers. Detailed architecture specifications, folder trees, component design patterns, styling standards, form validation, security requirements, and state management rules are maintained in dedicated reference files under `references/`.

---

## 🎯 Strict Directives vs. Flexible Guidelines

To maintain production excellence without imposing unnecessary rigidity:

### 🔒 STRICT & MANDATORY REQUIREMENTS (Non-Negotiable Production Necessities)
1. **Security Standards**:
   - **XSS Prevention**: Never use `dangerouslySetInnerHTML` without strict `DOMPurify` sanitization.
   - **Token Storage**: Never store sensitive auth JWT tokens in `localStorage`. Use `HttpOnly` cookies set by backend servers.
   - **Input Sanitization**: Validate and sanitize all client inputs before API dispatch.
2. **Form Validation & Formal UI Necessities**:
   - **Explicit Input Labels**: Every input MUST have an explicit `<label htmlFor="...">` or `aria-label`. Never rely on placeholders as labels.
   - **ARIA Linkage**: Link inputs to helper text and error alerts via `aria-describedby` and `aria-invalid`.
   - **Double-Submit Prevention**: Disable async action buttons and show loading spinners during active mutations.
   - **Action Micro-Copy**: Use explicit action verbs ("Save Profile" vs "Submit").
3. **Memory Leak Prevention & Cleanups**:
   - Every `useEffect` side effect (listeners, timers, subscriptions, fetch requests) MUST return a cleanup function or `AbortController.abort()`.

### 🎨 FLEXIBLE & ADAPTIVE GUIDANCE (Project Preference Guidelines)
- **Architecture & Directory Structure**: Feature-Driven, Next.js App Router, Layered, Clean, or Atomic Design are **flexible recommendations**. Adapt to the project's established structure or user preference without forcing refactoring.
- **State Management Tools**: Redux Toolkit, Zustand, TanStack Query, React Context, or Jotai are **flexible choices**. Align with whatever the project currently uses or requests.
- **Styling Libraries**: Tailwind CSS, CSS Modules, Styled Components, Emotion, Shadcn UI, or Material UI are **adaptable choices**. Inspect `package.json` and follow installed library docs per version.

---

## Agent Execution Workflow

```text
User Request
    ↓
1. Inspect Project Context (package.json dependencies, Redux/Zustand, Tailwind version, UI libraries)
    ↓
2. Check Installed Library & Framework Documentation per Version
    ↓
3. Adapt to Project's Established Architecture & State Preferences (Feature-Driven / Layered / App Router)
    ↓
4. Load & Read Relevant Architecture & Guideline References (references/)
    ↓
5. Plan Implementation (Decompose into small UI chunks & hooks; enforce mandatory security, validation & leak cleanups)
    ↓
6. Execute Implementation / Refactoring (Readable direct logic, action button micro-copy, zero vibe-code bloat)
    ↓
7. Check TypeScript Errors, Form ARIA Linkage, Memory Cleanups & Theme Token Adherence
    ↓
8. Verify Behavior & Run Build / Test Checks
```

---

## 1. Project Inspection Rules (Anti-Hallucination & Version Checking)

Before creating or editing code in a React project, gather empirical evidence:

1. **Inspect Framework & Routing**: Identify whether the project is Vite (SPA), Next.js App Router (`app/`), Next.js Pages Router (`pages/`), Remix (`app/routes`), or React Native.
2. **Inspect `package.json` & Library Versions**: Check exact versions of React, Redux Toolkit, Tailwind CSS, UI libraries (Shadcn, Radix, MUI, Ant Design, HeroUI), router, and state management.
3. **Verify UI Library & Redux Docs per Version**: Always write code compatible with the *specific installed version* of third-party UI components, Redux Toolkit (`configureStore`, `createSlice`), and Tailwind (v3 vs v4).
4. **Inspect Theme & Styling Rules**: Check `tailwind.config`, CSS variables, or theme providers. Use established theme tokens (`bg-background`, `text-primary`) rather than hardcoding magic hex values.
5. **Identify Established Conventions**: Match existing directory structures, file naming (`PascalCase.tsx`, `use-hook.ts`), and export patterns.

---

## 2. Architecture Selection & Reference Loading

Determine the target architecture based on project complexity and framework:

- **Next.js App Router Projects**: Use **Next.js App Router Architecture** (`nextjs-app-router.md`).
- **Medium / Large Production Apps**: Use **Feature-Driven (Vertical Slice) Architecture** by default (`feature-driven.md`).
- **Design Systems / UI Component Libraries**: Use **Atomic Design Architecture** (`atomic-design.md`).
- **Domain-Heavy Enterprise Apps**: Use **Clean Architecture (Hexagonal)** (`clean-architecture.md`).
- **Small SPAs & Prototypes**: Use **Layered Architecture** (`layered.md`).

### Architecture Reference Mapping

Load and read the target architecture specification before writing code:

| Architecture | Reference File Path | Primary Use Case |
| :--- | :--- | :--- |
| **Feature-Driven** *(Default)* | [`references/architectures/feature-driven.md`](./references/architectures/feature-driven.md) | Scalable production apps organized by domain capabilities |
| **Next.js App Router** | [`references/architectures/nextjs-app-router.md`](./references/architectures/nextjs-app-router.md) | Next.js 13+ RSC, Server Actions, Route Groups |
| **Layered** | [`references/architectures/layered.md`](./references/architectures/layered.md) | Standard SPAs organized by horizontal layers (`components`, `hooks`, `services`) |
| **Atomic Design** | [`references/architectures/atomic-design.md`](./references/architectures/atomic-design.md) | Design systems, component libraries, atomic breakdown |
| **Clean Architecture** | [`references/architectures/clean-architecture.md`](./references/architectures/clean-architecture.md) | Domain-driven apps with decoupled UI & core enterprise logic |

---

## 3. Operational Guidelines Index

Load relevant guideline files based on specific task requirements:

- **Security & Accessibility (a11y) [STRICT]**: [`references/guidelines/security-and-a11y.md`](./references/guidelines/security-and-a11y.md)
- **Form Validation & UI Micro-Details [STRICT]**: [`references/guidelines/forms-and-ui-microdetails.md`](./references/guidelines/forms-and-ui-microdetails.md)
- **Type Safety & Memory Leak Prevention [STRICT]**: [`references/guidelines/typescript.md`](./references/guidelines/typescript.md)
- **State Management & React Redux (RTK)**: [`references/guidelines/state-management.md`](./references/guidelines/state-management.md)
- **Data Fetching, Pagination & Loaders**: [`references/guidelines/data-fetching.md`](./references/guidelines/data-fetching.md)
- **Styling, Tailwind & Enterprise UI Rules**: [`references/guidelines/styling-and-ui.md`](./references/guidelines/styling-and-ui.md)
- **Folder & File Organization**: [`references/guidelines/folder-structure.md`](./references/guidelines/folder-structure.md)
- **Component Design & Composition**: [`references/guidelines/component-design.md`](./references/guidelines/component-design.md)
- **Performance & Optimization**: [`references/guidelines/performance.md`](./references/guidelines/performance.md)
- **Testing Strategy (Unit, UI, E2E)**: [`references/guidelines/testing.md`](./references/guidelines/testing.md)
- **Official Docs & Resources**: [`references/guidelines/official-docs-and-resources.md`](./references/guidelines/official-docs-and-resources.md)

---

## 4. Core Execution Rules

1. **Mandatory Security Rules**: Sanitize raw HTML with `DOMPurify`. Never store tokens in `localStorage`. Validate input mutations with Zod schemas.
2. **Production Form & Micro-Detail Standards**: Every input field MUST have an explicit label, `aria-describedby` helper instructions/errors, and proper `autoComplete`/`inputMode` attributes. Buttons MUST use explicit action verbs ("Save Changes" vs "Submit"), include `aria-label` for icon-only buttons, and disable during loading to prevent double-submits.
3. **Memory Leak Prevention & AbortController**: Every async fetch, event listener, interval, or subscription inside `useEffect` MUST be properly cleaned up or aborted (`controller.abort()`, `removeEventListener`, `clearInterval`).
4. **Adaptable State & Tooling Selection**: Respect the project's existing state libraries (Redux, Zustand, Context, Query) and styling tools. Do not force unnecessary migration if the project already has an established preference.
5. **Pagination & Page Loaders**: Synchronize pagination parameters in URL search params. Render Skeleton loaders for dynamic content to prevent layout shifts.
6. **Code Chunking into Custom Hooks**: Never embed complex state, async calls, and form logic directly inside components. Break code into custom hooks (`useFeatureName.ts`) and presentational UI components.
7. **Prioritize Code Readability & Direct Logic**: Keep logic transparent, self-explanatory, and direct. Avoid unnecessary dynamic indirection or convoluted helper abstractions.
8. **Clean Enterprise UI (No AI Vibe-Code Bloat)**: Design clean, accessible, user-friendly enterprise interfaces. Avoid floating glassmorphic cards, chaotic glowing gradients, or non-standard visual noise.

---

## 5. Verification Checklist

- [ ] Are mandatory security requirements met (XSS sanitization, HttpOnly token storage, input schema validation)?
- [ ] Do all form inputs have explicit `<label>` elements, `aria-describedby` helper text, and proper `inputMode`/`autoComplete` attributes?
- [ ] Do action buttons use explicit action-driven micro-copy ("Create Project" vs "Submit") and disable with a spinner during async loading?
- [ ] Are icon-only buttons annotated with explicit `title` tooltips and `aria-label` screen reader text?
- [ ] Are all `useEffect` side effects properly cleaned up (`AbortController`, `removeEventListener`) to prevent memory leaks?
- [ ] Has `package.json` been checked to verify exact versions of Redux Toolkit, Tailwind CSS, and UI libraries?
- [ ] Do build checks (`npm run build` / `tsc`) pass without error?
