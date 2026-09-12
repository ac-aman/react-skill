# Data Fetching, Pagination & Loading States

Data fetching in enterprise React applications must handle loading states, pagination, error boundaries, caching, background refetching, request cancellation, and optimistic updates smoothly.

---

## 🌐 1. Prefer Managed Caching Engines (TanStack Query / RTK Query)

Avoid writing manual `useEffect` + `useState` data fetching loops.

```tsx
import { useQuery } from '@tanstack/react-query';
import { fetchProducts } from '../api/products';

export function ProductList() {
  const { data, isLoading, isError, error } = useQuery({
    queryKey: ['products'],
    queryFn: fetchProducts,
    staleTime: 1000 * 60 * 5, // 5 minutes fresh cache
  });

  if (isLoading) return <ProductListSkeleton />;
  if (isError) return <ErrorMessage message={error.message} />;

  return (
    <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
      {data.map((p) => (
        <ProductCard key={p.id} product={p} />
      ))}
    </div>
  );
}
```

---

## 📄 2. Pagination Patterns & URL Search Params Sync

Keep pagination state (current page, page size, sort order) in the URL search parameters so users can share, bookmark, or refresh paginated lists without losing state.

### ✅ Paginated Query Pattern (Offset / Limit)
```tsx
import { useSearchParams } from 'react-router-dom'; // Or Next.js useSearchParams
import { keepPreviousData, useQuery } from '@tanstack/react-query';
import { fetchPaginatedUsers } from '../api/users';

export function PaginatedUserTable() {
  const [searchParams, setSearchParams] = useSearchParams();
  const page = Number(searchParams.get('page')) || 1;
  const pageSize = 10;

  const { data, isLoading, isFetching } = useQuery({
    queryKey: ['users', page, pageSize],
    queryFn: () => fetchPaginatedUsers({ page, limit: pageSize }),
    placeholderData: keepPreviousData, // Keeps old page UI visible while new page loads!
  });

  const handlePageChange = (newPage: number) => {
    searchParams.set('page', String(newPage));
    setSearchParams(searchParams);
  };

  if (isLoading) return <TableSkeleton rows={pageSize} />;

  return (
    <div className="space-y-4">
      <div className={`transition-opacity ${isFetching ? 'opacity-50' : 'opacity-100'}`}>
        <UserTable users={data?.users ?? []} />
      </div>

      {/* Pagination Controls */}
      <div className="flex items-center justify-between">
        <p className="text-sm text-muted-foreground">
          Page {page} of {data?.totalPages ?? 1}
        </p>
        <div className="flex gap-2">
          <button
            disabled={page === 1 || isFetching}
            onClick={() => handlePageChange(page - 1)}
            className="px-3 py-1 border rounded disabled:opacity-50"
          >
            Previous
          </button>
          <button
            disabled={page >= (data?.totalPages ?? 1) || isFetching}
            onClick={() => handlePageChange(page + 1)}
            className="px-3 py-1 border rounded disabled:opacity-50"
          >
            Next
          </button>
        </div>
      </div>
    </div>
  );
}
```

---

## ⌛ 3. UI Loaders & Loading State Standards

1. **Skeleton Loaders over Generic Centered Spinners**: Skeleton screens match the layout structure of incoming content, preventing layout shift (CLS) and giving users an instant perception of speed.
2. **Inline Fetching Indicators**: When refetching data in the background (e.g. `isFetching` in TanStack Query), show an inline subtle spinner or reduce opacity (`opacity-60`) rather than destroying the rendered UI.
3. **Empty States**: Always render an explicit empty state component when `data` is empty (`data.length === 0`).

```tsx
// Example Skeleton UI Loader
export function UserListSkeleton() {
  return (
    <div className="space-y-3 animate-pulse">
      {[1, 2, 3, 4, 5].map((i) => (
        <div key={i} className="h-12 bg-muted rounded-md w-full" />
      ))}
    </div>
  );
}
```

---

## 🚫 4. Request Cancellation & AbortController

Always pass the `signal` provided by your data fetching library or `useEffect` to raw `fetch` or `axios` calls to abort outdated network requests and prevent memory leaks.

```typescript
// TanStack Query automatically provides a signal argument in queryFn!
const fetchUsers = async ({ signal }: { signal?: AbortSignal }) => {
  const response = await fetch('/api/users', { signal });
  if (!response.ok) throw new Error('Network error');
  return response.json();
};
```

---

## 🔄 5. Optimistic UI Updates

Update the UI immediately before the backend server responds, rolling back if the request fails:

```typescript
import { useMutation, useQueryClient } from '@tanstack/react-query';

export function useUpdateTodo() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: updateTodoApi,
    onMutate: async (newTodo) => {
      await queryClient.cancelQueries({ queryKey: ['todos'] });
      const previousTodos = queryClient.getQueryData(['todos']);

      queryClient.setQueryData(['todos'], (old: Todo[] = []) =>
        old.map((t) => (t.id === newTodo.id ? { ...t, ...newTodo } : t))
      );

      return { previousTodos };
    },
    onError: (err, newTodo, context) => {
      queryClient.setQueryData(['todos'], context?.previousTodos);
    },
    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: ['todos'] });
    },
  });
}
```
