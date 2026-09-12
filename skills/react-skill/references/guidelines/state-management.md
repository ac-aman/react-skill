# State Management Strategy

State management in React should be divided clearly into **4 Categories**: Local State, Server State, Global Client State, and URL State.

---

## 📊 State Classification Matrix

| State Type | Description | Best Tool / Solution | Example |
| :--- | :--- | :--- | :--- |
| **Local State** | UI state isolated to a single component or form. | `useState`, `useReducer` | Modal open toggle, input field value |
| **Server State** | Asynchronous cached data originating from an external DB/API. | **TanStack Query** (React Query), RTK Query, SWR | User profile data, product lists |
| **Global Client State**| Synchronous UI state shared across multiple distant components. | **Zustand**, **Redux Toolkit (RTK)**, React Context, Jotai | User theme (dark/light), cart items, auth session |
| **URL State** | State reflected directly in the browser address bar. | `useSearchParams`, `useParams`, Nuqs | Search queries, filter selections, pagination page |

---

## 🚫 Critical Rule: Do NOT Mix Server State with Global State Store

A common architectural anti-pattern is manually managing raw API fetch responses in Redux or Zustand slices without automatic caching.

```text
❌ WRONG (Manual Fetching into Global Store):
  API Call → useEffect → dispatch(setProducts(data)) → Redux Store

✅ CORRECT (TanStack Query / RTK Query):
  API Call → Managed Caching Engine (Handles stale-time, automatic refetch, loading, errors, cancellation)
```

---

## ⚛️ 1. React Redux (Redux Toolkit / RTK) Standard Structure

If the project uses or requests **React Redux**, follow the official Redux Toolkit (RTK) structure and TypeScript conventions.

### 📁 Recommended Redux Toolkit Folder Structure
```text
src/
├── app/
│   ├── store.ts               # Centralized Redux configureStore
│   └── hooks.ts               # Typed hooks (useAppDispatch, useAppSelector)
│
├── features/
│   ├── counter/
│   │   ├── counterSlice.ts    # RTK createSlice (reducers, actions)
│   │   └── Counter.tsx        # UI component consuming Redux
│   └── auth/
│       ├── authSlice.ts
│       └── authApi.ts         # RTK Query API slice (if using RTK Query)
```

### 1️⃣ Typed Store Configuration (`src/app/store.ts`)
```typescript
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from '../features/counter/counterSlice';
import authReducer from '../features/auth/authSlice';

export const store = configureStore({
  reducer: {
    counter: counterReducer,
    auth: authReducer,
  },
});

// Infer RootState and AppDispatch types from the store itself
export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

### 2️⃣ Typed Redux Hooks (`src/app/hooks.ts`)
Always use custom typed hooks throughout the application instead of plain `useDispatch` and `useSelector`:
```typescript
import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux';
import type { RootState, AppDispatch } from './store';

// Use throughout your app instead of plain `useDispatch` and `useSelector`
export const useAppDispatch = () => useDispatch<AppDispatch>();
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

### 3️⃣ Feature Slice (`src/features/counter/counterSlice.ts`)
```typescript
import { createSlice, PayloadAction } from '@reduxjs/toolkit';
import type { RootState } from '../../app/store';

interface CounterState {
  value: number;
  status: 'idle' | 'loading' | 'failed';
}

const initialState: CounterState = {
  value: 0,
  status: 'idle',
};

export const counterSlice = createSlice({
  name: 'counter',
  initialState,
  reducers: {
    increment: (state) => {
      // Redux Toolkit uses Immer internally for safe state mutations
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action: PayloadAction<number>) => {
      state.value += action.payload;
    },
  },
});

export const { increment, decrement, incrementByAmount } = counterSlice.actions;

// Selector function
export const selectCount = (state: RootState) => state.counter.value;

export default counterSlice.reducer;
```

### 4️⃣ Component Usage with Typed Hooks (`src/features/counter/Counter.tsx`)
```tsx
import { useAppDispatch, useAppSelector } from '../../app/hooks';
import { increment, decrement, selectCount } from './counterSlice';

export function Counter() {
  const count = useAppSelector(selectCount);
  const dispatch = useAppDispatch();

  return (
    <div className="flex items-center gap-4">
      <button onClick={() => dispatch(decrement())}>-</button>
      <span>{count}</span>
      <button onClick={() => dispatch(increment())}>+</button>
    </div>
  );
}
```

---

## 🐻 2. Zustand (Lightweight Alternative for Global Client State)

Zustand is lightweight, fast, boilerplate-free, and avoids Context provider re-render cascade issues.

### Example Zustand Store (`features/cart/store/cart-store.ts`):
```typescript
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface CartItem {
  id: string;
  name: string;
  price: number;
  quantity: number;
}

interface CartState {
  items: CartItem[];
  addItem: (item: CartItem) => void;
  removeItem: (id: string) => void;
  clearCart: () => void;
}

export const useCartStore = create<CartState>()(
  persist(
    (set) => ({
      items: [],
      addItem: (item) =>
        set((state) => {
          const existing = state.items.find((i) => i.id === item.id);
          if (existing) {
            return {
              items: state.items.map((i) =>
                i.id === item.id ? { ...i, quantity: i.quantity + item.quantity } : i
              ),
            };
          }
          return { items: [...state.items, item] };
        }),
      removeItem: (id) =>
        set((state) => ({ items: state.items.filter((i) => i.id !== id) })),
      clearCart: () => set({ items: [] }),
    }),
    { name: 'shopping-cart-storage' }
  )
);
```

---

## ⚡ 3. React Context (Static / Low-Frequency Values Only)

React Context is designed for low-frequency updates like Theme, Current User Session, or Dependency Injection.

> ⚠️ **Warning**: React Context re-renders ALL consuming components whenever ANY value in the context object changes. For high-frequency state (e.g. text inputs, real-time counters), use Zustand, Redux Toolkit, or Jotai instead.

---

## 🔗 4. URL State (Search Params & Query Sync)

Keep shareable UI state (filters, sort order, search query, pagination page) in the URL search params so users can bookmark and share links:

```tsx
import { useSearchParams } from 'react-router-dom'; // Or Next.js useSearchParams

export function ProductFilter() {
  const [searchParams, setSearchParams] = useSearchParams();
  const category = searchParams.get('category') || 'all';

  const handleCategoryChange = (newCategory: string) => {
    searchParams.set('category', newCategory);
    setSearchParams(searchParams);
  };

  return (
    <select value={category} onChange={(e) => handleCategoryChange(e.target.value)}>
      <option value="all">All Categories</option>
      <option value="electronics">Electronics</option>
    </select>
  );
}
```
