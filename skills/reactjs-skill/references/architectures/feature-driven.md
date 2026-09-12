# Feature-Driven (Vertical Slice) Architecture

Feature-Driven (or Vertical Slice / Module-Based) Architecture organizes code around **business domains and product capabilities** rather than technical layer types. This is the **recommended default architecture** for production React applications across Vite, Next.js, Remix, and React Native.

---

## 💡 Core Principles

1. **Domain Isolation**: Each feature module (`features/auth`, `features/dashboard`, `features/checkout`) contains its own UI components, hooks, state, types, and API integrations.
2. **High Cohesion, Low Coupling**: Code that changes together lives together. A change to the checkout flow only touches files inside `features/checkout/`.
3. **Public API (Module Boundaries)**: Features expose internal functionality exclusively via an `index.ts` barrier file. Internal helpers stay hidden.
4. **Shared Directory Discipline**: Common UI primitives (buttons, inputs, modals) and cross-cutting utilities live in `shared/` or `components/ui/`, never inside a specific feature.

---

## 📁 Recommended Folder Structure

```text
src/
├── app/                        # App setup, providers, global styles, router config
│   ├── providers/              # React Context / QueryClient / Router providers
│   ├── router/                 # App routes definition (if using Vite/React Router)
│   ├── styles/                 # Global CSS, Tailwind base, CSS variables
│   └── App.tsx                 # Root application component
│
├── features/                   # Business domain modules (Vertical Slices)
│   ├── auth/                   # Authentication Domain
│   │   ├── api/                # Feature API calls & TanStack Query mutations/queries
│   │   │   ├── login.ts
│   │   │   └── use-user.ts
│   │   ├── components/         # Feature-specific UI components
│   │   │   ├── LoginForm.tsx
│   │   │   └── AuthGuard.tsx
│   │   ├── hooks/              # Feature custom hooks
│   │   │   └── use-auth.ts
│   │   ├── store/              # Feature state slice (e.g. Zustand slice)
│   │   │   └── auth-store.ts
│   │   ├── types/              # Feature domain types & contracts
│   │   │   └── index.ts
│   │   ├── utils/              # Feature-specific helper logic
│   │   └── index.ts            # Public API barrier (exports components, hooks, types)
│   │
│   ├── products/               # Product Catalog Domain
│   │   ├── api/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── types/
│   │   └── index.ts
│   │
│   └── cart/                   # Shopping Cart Domain
│       ├── api/
│       ├── components/
│       ├── hooks/
│       ├── store/
│       └── index.ts
│
├── shared/                     # Cross-cutting assets used by multiple features
│   ├── components/             # Generic UI Primitives (Button, Modal, Input, Card)
│   │   └── ui/
│   ├── hooks/                  # Global custom hooks (useDebounce, useMediaQuery)
│   ├── lib/                    # External library configs (axios instance, query-client)
│   ├── types/                  # Global/Shared TypeScript definitions
│   └── utils/                  # Utility functions (formatting, validation, helpers)
│
├── assets/                     # Static assets (images, icons, fonts, SVGs)
└── main.tsx                    # Application entry point
```

---

## 🔒 Public API Barrier Pattern (`index.ts`)

Every feature module must expose a strict public interface via `index.ts`. Other features or pages must **only import from the root of the feature module**.

### Example: `features/auth/index.ts`
```typescript
// Public API exports for Auth Feature
export { LoginForm } from './components/LoginForm';
export { AuthGuard } from './components/AuthGuard';
export { useAuth } from './hooks/use-auth';
export type { User, LoginCredentials } from './types';
```

### Direct vs. Module Imports
```typescript
// ❌ WRONG: Reaching into private feature internals from another feature
import { LoginForm } from '@/features/auth/components/LoginForm';
import { authApi } from '@/features/auth/api/login';

// ✅ CORRECT: Importing via public feature entry point
import { LoginForm, useAuth } from '@/features/auth';
```

---

## ⚡ Framework Variations

### 1. Next.js (App Router Integration)
In Next.js, place route files in `app/` and delegate render logic to feature modules in `features/`:
```text
app/
├── (auth)/
│   └── login/
│       └── page.tsx           # Simple page wrapping <LoginForm /> from @/features/auth
└── dashboard/
    └── page.tsx               # Simple page wrapping <DashboardView /> from @/features/dashboard
```

### 2. Remix / React Router v7 Integration
Routes under `app/routes/` import components and loaders/actions from `features/`:
```typescript
// app/routes/login.tsx
import { LoginForm, useAuth } from '@/features/auth';

export default function LoginPage() {
  return <LoginForm />;
}
```

---

## ✅ Best Practices Checklist

- [ ] Does every domain feature live inside `features/<feature-name>`?
- [ ] Is there an `index.ts` exporting only public APIs for each feature?
- [ ] Are generic UI components (Buttons, Inputs, Dialogs) located in `shared/components/ui/`?
- [ ] Are cross-feature dependencies flowing through public APIs without circular imports?
