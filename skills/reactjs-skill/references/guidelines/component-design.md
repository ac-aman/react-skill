# Component Design, Composition & Code Chunking

Building enterprise React applications requires modular, readable components with clear separation between UI rendering and stateful logic.

---

## 📖 1. Code Readability & Direct Logic First

1. **Prioritize Human Readability**: Write transparent, self-documenting code. Prefer explicit, direct logic over complex indirection or clever dynamic abstractions.
2. **Predictable Control Flow**: Avoid deeply nested ternary operators (`a ? b ? c : d : e`). Use early returns or explicit sub-render variables.
3. **Cohesive File Sizes**: Aim to keep component files focused and concise (ideally under ~100-150 lines). If a file grows large, break it into smaller cohesive sub-components and custom hooks.

---

## ✂️ 2. Code Decomposition & Custom Hooks Chunking

Never construct monolithic components that embed data fetching, state transitions, validation, and complex JSX in one place.

```text
❌ Monolithic Component (Data fetching + state + handlers + 200 lines of JSX)
                  │
                  ▼
✅ Decomposed Architecture:
  ├── Custom Hook (Extracts state, queries, and handlers into `useFeatureName.ts`)
  ├── Container Component (Wires hook to sub-components)
  └── Sub-Components (Small, readable UI pieces)
```

### Example Decomposition

#### ❌ Bad: Monolithic Component
```tsx
// ❌ WRONG: Inline state, fetching, handlers, and giant rendering block
export function UserProfilePage() {
  const [user, setUser] = useState(null);
  const [isEditing, setIsEditing] = useState(false);
  const [formData, setFormData] = useState({});
  // ... 150 lines of handlers, inline fetch calls, and JSX layout ...
}
```

#### ✅ Good: Clean Decomposed Chunking
```tsx
// 1. Custom Hook: Extract state & handlers (src/features/user/hooks/useUserProfile.ts)
export function useUserProfile(userId: string) {
  const { data: user, isLoading } = useQuery({ queryKey: ['user', userId], queryFn: () => fetchUser(userId) });
  const [isEditing, setIsEditing] = useState(false);

  const toggleEdit = () => setIsEditing((prev) => !prev);

  return { user, isLoading, isEditing, toggleEdit };
}

// 2. Readable Presentational Component (src/features/user/components/UserProfilePage.tsx)
export function UserProfilePage({ userId }: { userId: string }) {
  const { user, isLoading, isEditing, toggleEdit } = useUserProfile(userId);

  if (isLoading) return <UserProfileSkeleton />;
  if (!user) return <EmptyState message="User not found" />;

  return (
    <div className="space-y-6 max-w-4xl mx-auto p-6">
      <UserProfileHeader user={user} isEditing={isEditing} onToggleEdit={toggleEdit} />
      {isEditing ? <UserProfileForm user={user} /> : <UserProfileDetails user={user} />}
    </div>
  );
}
```

---

## 🎨 3. Enterprise Clean UI Principles vs. "AI Vibe-Code" Bloat

- **Clean & Functional UI**: Build user-friendly, enterprise-grade interfaces designed for clarity, high readability, and smooth user workflows.
- **Avoid "Vibe Code" Bloat**: Do NOT generate random floating cards, chaotic glowing gradients, unnecessary animated widgets, or arbitrary non-standard colors.
- **Stick to Theme Tokens**: Respect the defined design system (`bg-background`, `text-primary`, `border-border`).
- **Use Installed UI Libraries**: If the project has an installed component library (Shadcn UI, Radix, Material UI, Ant Design, etc.), inspect its installed version and use its native components.

---

## 🧱 4. Compound Component Pattern

Use Compound Components when creating complex, cohesive UI widgets (e.g. Modals, Selects, Accordions, Tabs) that share implicit state:

```tsx
<Accordion defaultValue="item-1">
  <Accordion.Item value="item-1">
    <Accordion.Header>Is this framework agnostic?</Accordion.Header>
    <Accordion.Content>Yes! Works with Vite, Next.js, and Remix.</Accordion.Content>
  </Accordion.Item>
</Accordion>
```

---

## 🛡️ 5. Props Design & Best Practices

1. **Explicit Props Interfaces**: Define clear TypeScript interfaces for props. Avoid `any` or loose `Record<string, any>`.
2. **Handler Naming Standards**: Name event handler props with `on` prefix (`onSelect`, `onChange`, `onSubmit`). Name internal handler functions with `handle` prefix (`handleSelect`, `handleSubmit`).
3. **Keep Props Flat**: Pass flat primitives or focused domain objects rather than deeply nested structures.
