# Testing Strategy (Unit, Component, Integration & E2E)

A reliable testing pyramid ensures high confidence, fast feedback loops, and zero regression bugs in production React apps.

---

## 🔺 The Testing Pyramid

```text
       / \
      / E2E \         ← Critical User Journeys (Playwright / Cypress)
     /-------\
    / Component\      ← UI Interactions & Render Logic (React Testing Library + Vitest)
   /------------\
  /  Unit Tests  \    ← Domain Logic, Utils, Custom Hooks (Vitest / Jest)
 /----------------\
```

---

## 🧪 1. Unit Testing (Domain & Custom Hooks)

Use **Vitest** for unit tests. Test custom hooks using `@testing-library/react`:

```typescript
// hooks/useCounter.test.ts
import { renderHook, act } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import { useCounter } from './useCounter';

describe('useCounter', () => {
  it('increments counter value correctly', () => {
    const { result } = renderHook(() => useCounter(0));

    act(() => {
      result.current.increment();
    });

    expect(result.current.count).toBe(1);
  });
});
```

---

## 🧩 2. Component Testing (React Testing Library)

Focus tests on **user behavior** (clicking buttons, typing text) rather than implementation details (state variable names or internal method calls).

```tsx
// components/LoginForm.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { describe, it, expect, vi } from 'vitest';
import { LoginForm } from './LoginForm';

describe('<LoginForm />', () => {
  it('submits user credentials on valid form submission', async () => {
    const user = userEvent.setup();
    const handleSubmit = vi.fn();

    render(<LoginForm onSubmit={handleSubmit} />);

    // Query elements by visible role or text (Accessible queries)
    await user.type(screen.getByRole('textbox', { name: /email/i }), 'user@example.com');
    await user.type(screen.getByLabelText(/password/i), 'securepassword');
    await user.click(screen.getByRole('button', { name: /log in/i }));

    expect(handleSubmit).toHaveBeenCalledWith({
      email: 'user@example.com',
      password: 'securepassword',
    });
  });
});
```

---

## 🎭 3. End-to-End (E2E) Testing with Playwright

Test full user workflows across real browser engines:

```typescript
// e2e/checkout.spec.ts
import { test, expect } from '@playwright/test';

test('user can add item to cart and complete checkout', async ({ page }) => {
  await page.goto('/products');

  // Click first product card
  await page.click('text=Wireless Headphones');
  await page.click('button:has-text("Add to Cart")');

  // Navigate to Cart
  await page.click('a[href="/cart"]');
  await expect(page.locator('.cart-item-title')).toContainText('Wireless Headphones');

  // Proceed to Checkout
  await page.click('button:has-text("Checkout")');
  await expect(page).toHaveURL('/checkout');
});
```
