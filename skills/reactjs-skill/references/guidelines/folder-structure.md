# Folder Structure & File Naming Conventions

Consistent folder structures and predictable file naming are critical for maintainability, developer navigation, and automated AI refactoring.

---

## 📁 Standard Naming Conventions

| Entity | Case Convention | Example |
| :--- | :--- | :--- |
| **React Components** | `PascalCase.tsx` | `UserProfileCard.tsx`, `LoginForm.tsx` |
| **Custom Hooks** | `camelCase.ts` or `kebab-case.ts` | `useAuth.ts`, `use-debounce.ts` |
| **Utility / Helper Files** | `camelCase.ts` or `kebab-case.ts` | `formatCurrency.ts`, `date-utils.ts` |
| **Feature Directories** | `kebab-case` | `features/user-profile/`, `features/auth/` |
| **Styles / CSS Modules** | `Component.module.css` | `Button.module.css` |
| **Types & Interfaces** | `camelCase.ts` or `index.ts` | `types.ts`, `auth-types.ts` |
| **Test Files** | `Name.test.tsx` or `Name.spec.ts` | `Button.test.tsx`, `useAuth.test.ts` |

---

## 🎯 Component Co-location Pattern

Code that belongs exclusively to a single component should live right next to that component in a dedicated component directory:

```text
UserProfileCard/
├── UserProfileCard.tsx        # Component UI rendering logic
├── UserProfileCard.module.css # Component specific styles (or Tailwind)
├── UserProfileCard.test.tsx   # Component unit/integration test
├── UserProfileCard.stories.tsx# Storybook UI documentation
├── useUserProfileData.ts      # Component specific hook (if complex)
└── index.ts                   # Named re-export (`export { UserProfileCard } from './UserProfileCard'`)
```

---

## 🚫 Common Anti-Patterns to Avoid

1. **Massive Dumping Grounds**: Placing 50+ components flat inside a top-level `src/components/` directory without categorization.
2. **Deep Barrel File Imports**: Deeply reaching past module boundaries like `import { x } from '../../features/auth/components/internal/sub/Item'`. Use public `index.ts` barrier exports.
3. **Premature Abstraction**: Creating generic `/utils` or `/hooks` for single-use logic. Keep logic local until it is actually shared across 2 or more distinct locations.
4. **Inconsistent Extension Usage**: Mixing `.js`, `.jsx`, `.ts`, and `.tsx` arbitrarily. Use `.tsx` exclusively for files containing JSX markup, and `.ts` for pure TypeScript logic.
