# TypeScript Standards, Type Safety & Memory Leak Prevention

Strict TypeScript usage eliminates runtime errors, improves IDE autocomplete, and enforces robust component contracts while preventing memory leaks and state corruption.

---

## ⚙️ 1. Required `tsconfig.json` Compiler Options

Always enable strict type checking in enterprise projects:

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "exactOptionalPropertyTypes": true,
    "jsx": "react-jsx"
  }
}
```

---

## 🧩 2. Type Safety Rules & Anti-Patterns

1. **Zero Use of `any`**: Never use `any`. Use `unknown` for values of unverified shape, and validate them with Zod or type guards before dereferencing.
2. **Explicit Component Props**: Define explicit `interface` or `type` contracts for every component. Always type `children` explicitly using `ReactNode`.
3. **Strict Generic Component Constraints**: When building reusable generic components, constrain generic parameters (`<T extends Record<string, unknown>>`).

```tsx
import { ReactNode, ComponentPropsWithoutRef } from 'react';

// Explicit Props Interface
export interface ModalProps extends ComponentPropsWithoutRef<'div'> {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  children: ReactNode;
}

export function Modal({ isOpen, onClose, title, children, className, ...props }: ModalProps) {
  if (!isOpen) return null;
  return (
    <div className={className} {...props}>
      <h2>{title}</h2>
      {children}
      <button onClick={onClose}>Close</button>
    </div>
  );
}
```

---

## 🧹 3. Memory Leak Prevention & Effect Cleanup Rules

Memory leaks in React applications occur when async operations, event listeners, intervals, or WebSocket subscriptions continue running after a component unmounts.

### ⚠️ Rule 1: Always Clean Up Event Listeners & Timers
Every `addEventListener`, `setInterval`, or `setTimeout` inside `useEffect` MUST have a corresponding cleanup function.

```tsx
// ❌ WRONG: Memory Leak! Event listener persists after unmount
useEffect(() => {
  window.addEventListener('resize', handleResize);
}, []);

// ✅ CORRECT: Cleaned up on unmount
useEffect(() => {
  window.addEventListener('resize', handleResize);
  return () => {
    window.removeEventListener('resize', handleResize);
  };
}, [handleResize]);
```

### ⚠️ Rule 2: Cancel Unfinished Async Fetch Requests (`AbortController`)
Prevent setting state on unmounted components and eliminate network race conditions by aborting fetch requests:

```tsx
useEffect(() => {
  const controller = new AbortController();

  async function loadData() {
    try {
      const response = await fetch(`/api/user/${userId}`, { signal: controller.signal });
      const data = await response.json();
      setUser(data);
    } catch (err: unknown) {
      if (err instanceof Error && err.name !== 'AbortError') {
        setError(err.message);
      }
    }
  }

  loadData();

  // Cleanup: Abort request if component unmounts or userId changes
  return () => controller.abort();
}, [userId]);
```

---

## 🎯 4. Discriminated Unions for State Safety

Use Discriminated Unions to model mutually exclusive state configurations without illegal states:

```typescript
export type AsyncState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error };

function RenderState({ state }: { state: AsyncState<User[]> }) {
  switch (state.status) {
    case 'idle': return <p>Ready</p>;
    case 'loading': return <Spinner />;
    case 'success': return <UserList users={state.data} />;
    case 'error': return <ErrorAlert error={state.error} />;
  }
}
```

---

## 🪝 5. Custom Hook Return Types

Use `as const` tuple returns or named interfaces for custom hook outputs:

```typescript
// Tuple return with `as const`
export function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);
  const toggle = useCallback(() => setValue((v) => !v), []);
  return [value, toggle] as const;
}

// Named Object interface
export interface UseUserResult {
  user: User | null;
  isLoading: boolean;
  error: Error | null;
  refetch: () => void;
}

export function useUser(userId: string): UseUserResult {
  // ...
  return { user, isLoading, error, refetch };
}
```
