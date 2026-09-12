# Layered (Horizontal) Architecture

Layered (Horizontal) Architecture organizes the codebase by **technical responsibilities and file types** (`components/`, `hooks/`, `services/`, `store/`, `pages/`).

This is a classic architecture pattern well-suited for **small-to-medium single page applications (SPAs), prototypes, internal tools, or single-developer React projects**.

---

## 💡 Core Principles

1. **Separation by Technical Type**: Code is grouped into horizontal layers according to what the file does (UI Component, Custom Hook, API Service, State Store).
2. **Top-Down Dependency Flow**: Higher-level layers (Pages/Views) depend on middle layers (Components, Hooks), which in turn depend on lower layers (Services, Utilities, Types).
3. **Simplicity & Predictability**: Easy for developers to navigate because every button lives in `components/`, every API call lives in `services/`, and every state store lives in `store/`.

---

## 📁 Recommended Directory Structure

```text
src/
├── components/                 # Reusable Presentational UI Components
│   ├── common/                 # Base UI primitives (Button, Modal, Input, Spinner)
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   └── Modal.tsx
│   ├── layout/                 # Layout structure (Header, Footer, Sidebar, Navbar)
│   │   ├── Header.tsx
│   │   └── Sidebar.tsx
│   └── domain/                 # Domain-aware composite components
│       ├── UserCard.tsx
│       └── ProductGrid.tsx
│
├── pages/ or views/            # Route level container components / views
│   ├── HomePage.tsx
│   ├── LoginPage.tsx
│   ├── ProductsPage.tsx
│   └── SettingsPage.tsx
│
├── hooks/                      # Custom React Hooks
│   ├── useAuth.ts
│   ├── useDebounce.ts
│   ├── useFetchProducts.ts
│   └── useLocalStorage.ts
│
├── services/                   # External API integration, HTTP client, WebSockets
│   ├── api.ts                  # Axios / Fetch client instance & interceptors
│   ├── authService.ts          # Auth API requests
│   └── productService.ts      # Product API requests
│
├── store/                      # Global state management (Zustand / Redux / Context)
│   ├── authStore.ts
│   └── cartStore.ts
│
├── types/                      # Global TypeScript type definitions
│   ├── auth.ts
│   ├── product.ts
│   └── api.ts
│
├── utils/                      # Pure helper functions & formatters
│   ├── formatDate.ts
│   ├── formatCurrency.ts
│   └── validation.ts
│
├── constants/                  # Application constants, route paths, config keys
│   └── routes.ts
│
├── assets/                     # Static assets (images, logos, fonts, icons)
│   └── react.svg
│
├── App.tsx                     # Main application routing & providers wrapper
└── main.tsx                    # React DOM root entry point
```

---

## 🔄 Layer Responsibilities & Data Flow

```text
       [ Page / View Layer ]
                 │
                 ▼
      [ Component Layer ]
                 │
                 ▼
       [ Custom Hook Layer ]
                 │
                 ▼
      [ Service / API Layer ]
                 │
                 ▼
   [ External API / Backend ]
```

1. **Pages / Views**: Handle route params, top-level layout composition, and page title/meta tags.
2. **Components**: Render UI, receive props, emit user actions.
3. **Hooks**: Encapsulate stateful logic, side effects (`useEffect`), and async operations.
4. **Services**: Execute raw network calls (`fetch`/`axios`) and return typed responses.

---

## ⚡ When to Migrate Away from Layered Architecture

As projects grow past ~30-40 components, horizontal directories (`components/`, `hooks/`, `services/`) can become crowded dumping grounds.

**Signs it is time to migrate to Feature-Driven Architecture:**
- Multiple team members work on different business domains simultaneously.
- Finding all code related to a single feature requires jumping between 5 different top-level folders.
- Deleting or refactoring a feature requires hunting for unused hooks, components, and services scattered across the project.

---

## ✅ Best Practices Checklist

- [ ] Are generic UI primitives separated from domain components inside `components/`?
- [ ] Are HTTP network calls isolated inside `services/` rather than inline in components?
- [ ] Is complex UI state extracted into custom hooks inside `hooks/`?
- [ ] Are type definitions centralized in `types/`?
