# Form Validation, UI Micro-Details & Production UX Standards

In enterprise production React applications, the difference between a amateur app and a polished product lies in the **tiny UI/UX details**: form validation, input labels, helper text, accessible ARIA linkage, action button micro-copy, and double-submit prevention.

---

## 📋 1. Form Validation (Zod + React Hook Form)

Always use schema-driven validation (**Zod** + **React Hook Form**) to ensure type safety, instant field validation, and clean error states.

### ❌ Production Anti-Patterns to Avoid:
- Validating forms only on submit without clearing errors as the user types.
- Alerting errors using `window.alert()` or unformatted console warnings.
- Disabling submit buttons silently without telling the user *why* the form is invalid.

### ✅ Production Form Pattern with Zod & React Hook Form:

```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import { Button } from '@/components/ui/button';

// 1. Define Production Zod Schema with clear error messages
export const RegisterSchema = z.object({
  fullName: z.string().min(2, 'Full name must be at least 2 characters'),
  email: z.string().email('Please enter a valid work email address'),
  password: z
    .string()
    .min(8, 'Password must be at least 8 characters')
    .regex(/[A-Z]/, 'Password must contain at least one uppercase letter')
    .regex(/[0-9]/, 'Password must contain at least one number'),
});

export type RegisterFormData = z.infer<typeof RegisterSchema>;

export function RegisterForm({ onSubmit }: { onSubmit: (data: RegisterFormData) => Promise<void> }) {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<RegisterFormData>({
    resolver: zodResolver(RegisterSchema),
    mode: 'onTouched', // Validates when the user interacts and leaves the field
  });

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-5 max-w-md">
      {/* Field 1: Full Name */}
      <div className="space-y-1.5">
        <label htmlFor="fullName" className="block text-sm font-medium text-foreground">
          Full Name <span className="text-destructive">*</span>
        </label>
        <input
          id="fullName"
          type="text"
          autoComplete="name"
          placeholder="e.g. Sarah Connor"
          aria-invalid={!!errors.fullName}
          aria-describedby={errors.fullName ? 'fullName-error' : 'fullName-help'}
          className="w-full px-3 py-2 border rounded-md focus:ring-2 focus:ring-primary text-sm"
          {...register('fullName')}
        />
        <p id="fullName-help" className="text-xs text-muted-foreground">
          Enter your legal first and last name.
        </p>
        {errors.fullName && (
          <p id="fullName-error" className="text-xs text-destructive font-medium" role="alert">
            {errors.fullName.message}
          </p>
        )}
      </div>

      {/* Field 2: Email */}
      <div className="space-y-1.5">
        <label htmlFor="email" className="block text-sm font-medium text-foreground">
          Work Email <span className="text-destructive">*</span>
        </label>
        <input
          id="email"
          type="email"
          inputMode="email"
          autoComplete="email"
          placeholder="name@company.com"
          aria-invalid={!!errors.email}
          aria-describedby={errors.email ? 'email-error' : undefined}
          className="w-full px-3 py-2 border rounded-md focus:ring-2 focus:ring-primary text-sm"
          {...register('email')}
        />
        {errors.email && (
          <p id="email-error" className="text-xs text-destructive font-medium" role="alert">
            {errors.email.message}
          </p>
        )}
      </div>

      {/* Action Button */}
      <Button type="submit" disabled={isSubmitting} className="w-full">
        {isSubmitting ? (
          <span className="flex items-center gap-2">
            <Spinner className="h-4 w-4 animate-spin" />
            Creating Account...
          </span>
        ) : (
          'Create Account'
        )}
      </Button>
    </form>
  );
}
```

---

## 🏷️ 2. Input Fields, Labels & Helper Descriptions

### Rule 1: Every Input MUST Have an Explicit Label
- Never use `placeholder` as a replacement for `<label>`. Placeholders disappear when the user types, leaving them with zero context.
- Use explicit `htmlFor` matching the input's `id`: `<label htmlFor="user-email">Email</label>`.

### Rule 2: Explicit Required vs. Optional Badges
- Mark mandatory fields explicitly with a colored asterisk (`*`) or text indicator.
- Mark optional fields explicitly with `(optional)` helper text to reduce form friction.

```tsx
<label htmlFor="phone" className="block text-sm font-medium text-foreground">
  Phone Number <span className="text-xs text-muted-foreground font-normal">(optional)</span>
</label>
```

### Rule 3: Accessible Helper Descriptions (`aria-describedby`)
- Link helper instructions or validation errors to the input using `aria-describedby="field-help field-error"`. Screen readers read these descriptions when the user focuses the field.

### Rule 4: Correct `autoComplete`, `type` & `inputMode` Attributes
- Mobile browsers use `type` and `inputMode` to display the optimal soft keyboard layout:
  - Email: `type="email" inputMode="email" autoComplete="email"`
  - Phone: `type="tel" inputMode="tel" autoComplete="tel"`
  - Currency / Numbers: `type="text" inputMode="decimal"`
  - Passwords: `autoComplete="current-password"` or `autoComplete="new-password"`
  - 2FA OTP Code: `inputMode="numeric" autoComplete="one-time-code"`

---

## 🔘 3. Action Buttons & Micro-Copy Standards

Buttons trigger primary actions and must communicate intent clearly and safely.

### Rule 1: Action-Driven Micro-Copy
Avoid generic vague button labels like "Submit", "OK", or "Click Here". Use explicit action verbs:

| Vague Label ❌ | Production Action-Driven Label ✅ |
| :--- | :--- |
| `Submit` | `Save Changes`, `Create Project`, `Send Invoice` |
| `OK` | `Confirm Deletion`, `Continue to Payment` |
| `Delete` | `Delete Workspace` |
| `Save` | `Save Profile` |

### Rule 2: Icon-Only Buttons Require `aria-label` & Tooltips
Icon-only buttons (e.g. trash icon, close button, edit gear) are completely invisible to screen readers unless given an explicit `aria-label`:

```tsx
// ❌ WRONG: Screen reader announces "Button" with zero context
<button onClick={onDelete}><TrashIcon /></button>

// ✅ CORRECT: Accessible icon button with title tooltip & aria-label
<button
  type="button"
  onClick={onDelete}
  title="Delete Item"
  aria-label="Delete item from shopping cart"
  className="p-2 text-muted-foreground hover:text-destructive rounded-md focus-visible:ring-2"
>
  <TrashIcon className="h-4 w-4" aria-hidden="true" />
</button>
```

### Rule 3: Preventing Double-Submit & Async Loading States
During asynchronous API mutations, buttons MUST:
1. Show an inline loading spinner.
2. Change text to present-continuous tense ("Saving...", "Deleting...", "Processing...").
3. Set `disabled={isLoading}` to prevent accidental duplicate API clicks.

```tsx
<Button type="submit" disabled={isLoading} aria-busy={isLoading}>
  {isLoading ? (
    <>
      <Spinner className="mr-2 h-4 w-4 animate-spin" aria-hidden="true" />
      <span>Saving Changes...</span>
    </>
  ) : (
    <span>Save Changes</span>
  )}
</Button>
```

---

## 🔍 4. Production "Tiny Details" Checklist

Before shipping any component or form to production, verify this checklist:

- [ ] **Auto-Focus First Input on Modal Open**: When a dialog or modal opens, automatically focus the first interactive input field (`autoFocus` or `ref.current.focus()`).
- [ ] **Escape Key & Backdrop Click**: Dialogs and dropdowns close gracefully when the user presses `Esc` or clicks outside.
- [ ] **Destructive Action Confirmations**: Destructive actions (deleting data, revoking access) are styled with `destructive` warning colors and require explicit confirmation modal dialogs.
- [ ] **Toast Feedback**: Successful mutations trigger a subtle toast notification ("Profile saved successfully!"). Failed mutations explain the error clearly ("Failed to update profile. Please check your network.").
- [ ] **Error Focus Scrolling**: Submitting an invalid form automatically scrolls the browser window to focus the *first invalid field*.
- [ ] **Disabled State Styling**: Disabled buttons and inputs have readable contrast (`opacity-50 cursor-not-allowed`) and cannot be clicked or key-navigated.
- [ ] **Hover & Focus States**: All interactive elements have distinct `:hover`, `:active`, and visible `:focus-visible` focus ring styles.
