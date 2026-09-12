# Performance & Optimization Guidelines

Optimizing React applications requires targeted improvements in re-rendering efficiency, bundle size reduction, asset loading, and DOM virtualization.

---

## ⚡ 1. Re-render Prevention Rules

1. **Keep Component State Local**: Moving state down to the lowest sub-tree component prevents parent components from triggering re-renders across the entire tree.
2. **Memoize Expensive Computations with `useMemo`**: Use `useMemo` for complex data filtering, sorting, or transformations on large arrays (>100 items).
3. **Memoize Callbacks Passed to Pure Child Components with `useCallback`**: Use `useCallback` when passing functions to child components wrapped in `React.memo`.

```tsx
// ✅ Correct useMemo & useCallback Usage
import { useMemo, useCallback, memo } from 'react';

const ProductItem = memo(function ProductItem({ item, onDelete }: { item: Product; onDelete: (id: string) => void }) {
  return (
    <div onClick={() => onDelete(item.id)}>
      {item.name} - ${item.price}
    </div>
  );
});

export function ProductContainer({ items, search }: { items: Product[]; search: string }) {
  // Memoized filter calculation
  const filteredItems = useMemo(() => {
    return items.filter((i) => i.name.toLowerCase().includes(search.toLowerCase()));
  }, [items, search]);

  // Memoized callback function reference
  const handleDelete = useCallback((id: string) => {
    apiDeleteProduct(id);
  }, []);

  return (
    <div>
      {filteredItems.map((item) => (
        <ProductItem key={item.id} item={item} onDelete={handleDelete} />
      ))}
    </div>
  );
}
```

---

## 📦 2. Code Splitting & Dynamic Imports

Lazy load heavy routes, modals, or charting libraries using React `lazy` and `Suspense`:

```tsx
import { lazy, Suspense } from 'react';

// Lazy load heavy chart library module only when rendered
const HeavyAnalyticsChart = lazy(() => import('./HeavyAnalyticsChart'));

export function AnalyticsSection({ showChart }: { showChart: boolean }) {
  if (!showChart) return null;

  return (
    <Suspense fallback={<div className="h-64 bg-slate-100 animate-pulse rounded-lg" />}>
      <HeavyAnalyticsChart />
    </Suspense>
  );
}
```

---

## 📜 3. DOM Virtualization for Large Lists

When rendering long lists (>500 items), use list virtualization libraries like **TanStack Virtual** or `react-window` to render only the DOM elements currently visible in the scroll viewport.

```tsx
import { useVirtualizer } from '@tanstack/react-virtual';
import { useRef } from 'react';

export function VirtualList({ rows }: { rows: string[] }) {
  const parentRef = useRef<HTMLDivElement>(null);

  const rowVirtualizer = useVirtualizer({
    count: rows.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 35,
  });

  return (
    <div ref={parentRef} className="h-96 overflow-auto">
      <div style={{ height: `${rowVirtualizer.getTotalSize()}px`, position: 'relative' }}>
        {rowVirtualizer.getVirtualItems().map((virtualItem) => (
          <div
            key={virtualItem.key}
            style={{
              position: 'absolute',
              top: 0,
              left: 0,
              width: '100%',
              height: `${virtualItem.size}px`,
              transform: `translateY(${virtualItem.start}px)`,
            }}
          >
            {rows[virtualItem.index]}
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## 🎯 4. Image & Font Optimization

- **Next.js**: Always use `next/image` to automatically generate Responsive WebP/AVIF formats and prevent Cumulative Layout Shift (CLS).
- **Vite / Webpack**: Preload critical fonts using `<link rel="preload">` or `@fontsource` packages.
