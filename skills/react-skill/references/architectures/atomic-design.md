# Atomic Design System Architecture

Atomic Design (originally coined by Brad Frost) breaks down React component hierarchies into 5 distinct levels: **Atoms, Molecules, Organisms, Templates, and Pages**.

This architecture pattern is best suited for **Design Systems, Shared Component Libraries, UI Kit packages, and brand-driven enterprise React platforms**.

---

## 🔬 The 5 Atomic Levels

```text
Atoms → Molecules → Organisms → Templates → Pages
```

| Level | Definition | Examples | React Responsibilities |
| :--- | :--- | :--- | :--- |
| **Atoms** | Basic building blocks that cannot be broken down further without losing function. | `Button`, `Input`, `Label`, `Avatar`, `Icon`, `Spinner` | Pure styling, strict prop types, zero side effects or business state. |
| **Molecules** | Simple combinations of two or more Atoms working together as a functional unit. | `FormField` (Label + Input + Error), `SearchBar` (Input + Button), `UserChip` | Layout composition of Atoms, local presentation state. |
| **Organisms** | Complex, distinct UI sections composed of Molecules, Atoms, and other Organisms. | `Header`, `ProductCard`, `LoginForm`, `DataTable`, `Sidebar` | Handles section-level interaction, may accept data models or hooks. |
| **Templates** | Page-level layout skeletons that place Organisms into page structures without real content. | `DashboardLayout`, `AuthLayout`, `TwoColumnLayout` | Accepts slots/children or props to lay out Organisms. |
| **Pages** | Concrete instances of Templates filled with real dynamic data and context. | `DashboardPage`, `UserProfilePage`, `CheckoutPage` | Connects state providers, data fetching, route parameters. |

---

## 📁 Recommended Directory Structure

```text
src/
├── components/
│   ├── atoms/                  # 🟢 Atoms (Primitive elements)
│   │   ├── Button/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.stories.tsx
│   │   │   ├── Button.test.tsx
│   │   │   └── index.ts
│   │   ├── Input/
│   │   ├── Badge/
│   │   └── Typography/
│   │
│   ├── molecules/              # 🟡 Molecules (Simple combinations of Atoms)
│   │   ├── FormField/
│   │   │   ├── FormField.tsx
│   │   │   └── index.ts
│   │   ├── SearchBar/
│   │   └── DropdownMenu/
│   │
│   ├── organisms/              # 🔴 Organisms (Complex UI sections)
│   │   ├── NavigationBar/
│   │   ├── UserProfileCard/
│   │   ├── ProductGrid/
│   │   └── Footer/
│   │
│   └── templates/              # 🟣 Templates (Page-level layout skeletons)
│       ├── MainLayout/
│       ├── AuthLayout/
│       └── DashboardLayout/
│
├── pages/                      # 🔵 Pages (Dynamic route instances)
│   ├── HomePage.tsx
│   └── SettingsPage.tsx
│
├── styles/                     # Design tokens, variables, Tailwind theme
│   ├── tokens.ts               # Colors, typography scales, spacing tokens
│   └── globals.css
│
└── index.ts                    # Design System package library entry point
```

---

## 🧩 Component Breakdown Example

### 1. Atom (`atoms/Button/Button.tsx`)
```tsx
import { ButtonHTMLAttributes } from 'react';
import { cva, VariantProps } from 'class-variance-authority';

const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-md font-medium transition-colors focus:outline-none',
  {
    variants: {
      variant: {
        primary: 'bg-primary text-white hover:bg-primary/90',
        outline: 'border border-input bg-transparent hover:bg-accent',
      },
      size: {
        sm: 'h-8 px-3 text-xs',
        md: 'h-10 px-4 text-sm',
        lg: 'h-12 px-6 text-base',
      },
    },
    defaultVariants: { variant: 'primary', size: 'md' },
  }
);

export interface ButtonProps
  extends ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}

export function Button({ variant, size, className, ...props }: ButtonProps) {
  return <button className={buttonVariants({ variant, size, className })} {...props} />;
}
```

### 2. Molecule (`molecules/FormField/FormField.tsx`)
```tsx
import { Button } from '../../atoms/Button/Button';

interface SearchBarProps {
  onSearch: (query: string) => void;
  placeholder?: string;
}

export function SearchBar({ onSearch, placeholder = 'Search...' }: SearchBarProps) {
  return (
    <form
      onSubmit={(e) => {
        e.preventDefault();
        const formData = new FormData(e.currentTarget);
        onSearch(formData.get('q') as string);
      }}
      className="flex gap-2"
    >
      <input name="q" placeholder={placeholder} className="px-3 py-2 border rounded-md" />
      <Button variant="primary" size="md" type="submit">
        Search
      </Button>
    </form>
  );
}
```

---

## ✅ Best Practices Checklist

- [ ] Are Atoms completely free of domain business logic and API state?
- [ ] Are Molecules composed strictly of Atoms and other Molecules?
- [ ] Are Component Storybook / documentation stories maintained alongside components?
- [ ] Are design tokens (colors, spacing, fonts) centralized in `styles/tokens.ts`?
