# Next.js App Router Architecture

The Next.js App Router (introduced in Next.js 13+ and refined in 14/15) is built around **React Server Components (RSC)**, streaming, Server Actions, and file-system routing.

This reference specifies the ideal architecture for enterprise Next.js App Router applications.

---

## 💡 Core Concepts & Rules

1. **Server Components First (RSC)**: Default to React Server Components (`.tsx` without `'use client'`). Server components run only on the server, have direct DB/backend access, and add zero KB to the JavaScript bundle size.
2. **Client Components at Leaf Nodes**: Move `'use client'` down to the smallest interactive leaf elements (e.g. interactive buttons, forms, stateful toggles). Never mark an entire page as `'use client'` unless necessary.
3. **Server Actions for Mutations**: Use React 19 / Next.js Server Actions (`'use server'`) for form submissions, state mutations, and API calls with Zod schema validation.
4. **Route Groups & Parallel Routes**: Use `(group)` folders to organize routes logically without affecting URL structure.
5. **Colocation of Feature Logic**: Keep feature modules outside the `app/` directory (e.g. in `modules/` or `features/`) to decouple routing from domain business logic.

---

## 📁 Recommended Directory Structure

```text
my-next-app/
├── app/                        # Next.js File-System Routing Only
│   ├── (auth)/                 # Route Group: Auth layout & pages (URL: /login, /register)
│   │   ├── layout.tsx
│   │   ├── login/
│   │   │   └── page.tsx
│   │   └── register/
│   │       └── page.tsx
│   │
│   ├── (dashboard)/            # Route Group: Authenticated App Shell
│   │   ├── layout.tsx          # Shared Dashboard Navigation & Sidebar
│   │   ├── dashboard/
│   │   │   ├── page.tsx        # Server Component page fetching data directly
│   │   │   └── loading.tsx     # Suspense boundary fallback for instant loading state
│   │   └── settings/
│   │       └── page.tsx
│   │
│   ├── api/                    # Next.js Route Handlers (REST Webhooks / External APIs)
│   │   └── webhooks/
│   │       └── stripe/
│   │           └── route.ts
│   │
│   ├── error.tsx               # Global Error Boundary fallback component
│   ├── global-error.tsx        # Root HTML Error Boundary fallback component
│   ├── layout.tsx              # Root HTML & Body Layout with Font & Theme Providers
│   ├── loading.tsx             # Root Loading UI
│   ├── not-found.tsx           # Custom 404 page
│   └── page.tsx                # Landing / Home Page
│
├── src/                        # Application Codebase & Domain Modules
│   ├── actions/                # Global or Shared Server Actions ('use server')
│   │   └── auth-actions.ts
│   │
│   ├── features/               # Feature Modules (Vertical Slices)
│   │   ├── analytics/
│   │   ├── billing/
│   │   └── user-profile/
│   │       ├── actions/        # Feature-specific Server Actions
│   │       ├── components/     # Server & Client UI Components
│   │       ├── queries/        # Server-side DB queries / API fetchers
│   │       ├── types/          # Domain TypeScript types
│   │       └── index.ts        # Module Public API barrier
│   │
│   ├── components/             # Shared Design System Components
│   │   ├── ui/                 # Atomic UI primitives (Shadcn UI / Radix primitives)
│   │   │   ├── button.tsx
│   │   │   ├── dialog.tsx
│   │   │   └── input.tsx
│   │   └── shared/             # Layout components (Header, Footer, Navbar)
│   │
│   ├── db/                     # Database ORM connection & schemas (Prisma / Drizzle)
│   │   ├── index.ts
│   │   └── schema.ts
│   │
│   ├── lib/                    # Shared Utilities & Libraries
│   │   ├── auth.ts             # Auth configuration (NextAuth / Auth.js / Clerk)
│   │   ├── utils.ts            # Helper functions (cn, formatDate)
│   │   └── validation.ts       # Zod schemas
│   │
│   └── types/                  # Global TypeScript Interfaces
│
├── public/                     # Static public files (favicon, logo, og-image)
├── next.config.mjs             # Next.js configuration
├── tailwind.config.ts          # Tailwind CSS configuration
└── tsconfig.json               # TypeScript strict configuration
```

---

## ⚡ Server vs. Client Boundary Patterns

### ✅ Server Component (Data Fetching Page)
```tsx
// app/(dashboard)/dashboard/page.tsx
import { Suspense } from 'react';
import { getDashboardData } from '@/features/analytics/queries';
import { AnalyticsChart } from '@/features/analytics/components/AnalyticsChart';
import { MetricsSkeleton } from '@/features/analytics/components/MetricsSkeleton';

// Server Component (Async)
export default async function DashboardPage() {
  const data = await getDashboardData(); // Fetches directly on server without HTTP roundtrip

  return (
    <main className="p-6 space-y-6">
      <h1 className="text-2xl font-bold">Dashboard</h1>
      
      {/* Streaming with React Suspense */}
      <Suspense fallback={<MetricsSkeleton />}>
        <AnalyticsChart initialData={data} />
      </Suspense>
    </main>
  );
}
```

### ✅ Client Component (Leaf Interactivity)
```tsx
// src/features/analytics/components/AnalyticsChart.tsx
'use client'; // Interactivity (Chart JS / State) requires Client Component

import { useState } from 'react';

interface Props {
  initialData: MetricPoint[];
}

export function AnalyticsChart({ initialData }: Props) {
  const [filter, setFilter] = useState<'day' | 'month'>('day');

  return (
    <div className="bg-card p-4 rounded-xl shadow">
      <div className="flex justify-between mb-4">
        <button onClick={() => setFilter('day')}>Daily</button>
        <button onClick={() => setFilter('month')}>Monthly</button>
      </div>
      {/* Render Interactive Chart */}
    </div>
  );
}
```

---

## 🛡️ Server Actions & Type-Safe Mutation Pattern

```typescript
// src/features/user-profile/actions/update-profile.ts
'use server';

import { z } from 'zod';
import { db } from '@/db';
import { revalidatePath } from 'next/cache';

const UpdateProfileSchema = z.object({
  name: z.string().min(2, 'Name must be at least 2 characters'),
  bio: z.string().max(200, 'Bio too long'),
});

export async function updateProfileAction(formData: FormData) {
  const parsed = UpdateProfileSchema.safeParse({
    name: formData.get('name'),
    bio: formData.get('bio'),
  });

  if (!parsed.success) {
    return { success: false, errors: parsed.error.flatten().fieldErrors };
  }

  await db.user.update({
    where: { id: 'current-user-id' },
    data: parsed.data,
  });

  revalidatePath('/dashboard/settings');
  return { success: true };
}
```

---

## ✅ Best Practices Checklist

- [ ] Are pages and layouts Server Components by default?
- [ ] Is `'use client'` restricted strictly to interactive leaf components?
- [ ] Is routing kept thin inside `app/`, delegating business logic to `src/features/`?
- [ ] Are data mutations handled via type-safe Server Actions or API Route Handlers?
- [ ] Are dynamic loading states handled with `loading.tsx` and React `Suspense` boundaries?
