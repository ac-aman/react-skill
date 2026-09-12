# Security & Accessibility (a11y) Strict Production Standards

Security, data privacy, and accessible form controls are **non-negotiable strict requirements** for production React applications.

---

## 🔒 1. Strict Security Requirements (MANDATORY)

### 🚨 Rule 1: XSS Prevention & HTML Sanitization
- **Strict Rule**: NEVER use `dangerouslySetInnerHTML` directly with unsanitized user or third-party input.
- **Enforcement**: If raw HTML rendering is required, sanitize it strictly using `DOMPurify` before injecting it into the DOM.

```tsx
// 🛑 CRITICAL SECURITY RISK (Do NOT do this)
<div dangerouslySetInnerHTML={{ __html: userProvidedContent }} />

// ✅ STRICT COMPLIANT
import DOMPurify from 'dompurify';

<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userProvidedContent) }} />
```

---

### 🔑 Rule 2: Secure Authentication Token Storage
- **Strict Rule**: NEVER store sensitive authentication JWT tokens or API secrets in `localStorage` or `sessionStorage` (which are vulnerable to XSS script exfiltration).
- **Enforcement**: Session tokens MUST be transmitted via `HttpOnly`, `Secure`, `SameSite=Strict` (or `Lax`) HTTP cookies set by the backend server, or managed via encrypted memory session handlers.

---

### 🛡️ Rule 3: Client-Side Input Sanitization & Mutation Validation
- **Strict Rule**: Every client-side mutation (form submission, search query, data edit) MUST be validated against a strict schema (e.g. Zod) before network dispatch.
- **Enforcement**: Reject unexpected properties, enforce maximum length constraints, and trim whitespace on string inputs.

---

### ⚠️ Rule 4: Sensitive Data Masking & Leak Prevention
- **Strict Rule**: Sensitive credentials (passwords, API keys, credit card numbers, SSNs) must NEVER be logged to `console.log()` or exposed in plain text in client state/URL parameters.
- **Enforcement**: Mask sensitive inputs (`type="password"`), redact logs in production builds (`process.env.NODE_ENV === 'production'`), and use `autoComplete="current-password"` / `autoComplete="new-password"`.

---

## ♿ 2. Formal UI & Accessibility (a11y) Necessities (MANDATORY)

### 🏷️ Rule 1: Explicit Input Labeling & ARIA Linkage
- **Strict Rule**: Every interactive input element MUST have an associated `<label htmlFor="id">` or explicit `aria-label`.
- **Enforcement**: Placeholders disappear when typing and CANNOT serve as a label substitute. Use `aria-describedby` to link helper messages and error alerts.

```tsx
<div className="space-y-1">
  <label htmlFor="user-email" className="block text-sm font-medium">
    Work Email <span className="text-destructive" aria-hidden="true">*</span>
  </label>
  <input
    id="user-email"
    type="email"
    aria-invalid={!!error}
    aria-describedby={error ? 'email-error' : 'email-hint'}
    className="w-full px-3 py-2 border rounded-md"
  />
  <p id="email-hint" className="text-xs text-muted-foreground">
    We will send your verification link here.
  </p>
  {error && (
    <p id="email-error" className="text-xs text-destructive font-medium" role="alert">
      {error}
    </p>
  )}
</div>
```

---

### 🔘 Rule 2: Icon Button Accessibility
- **Strict Rule**: Icon-only buttons must NEVER be rendered without screen-reader text.
- **Enforcement**: Always add `aria-label="Action Name"` or a visually hidden `<span className="sr-only">Action Name</span>` tag.

```tsx
<button
  type="button"
  aria-label="Delete items"
  title="Delete item"
  className="p-2 rounded hover:bg-muted focus-visible:ring-2"
>
  <TrashIcon className="h-4 w-4" aria-hidden="true" />
</button>
```

---

### ⌨️ Rule 3: Keyboard Focus Visibility
- **Strict Rule**: Focus rings must NEVER be stripped using `outline: none` without providing a clear replacement.
- **Enforcement**: All interactive controls must feature prominent focus styling (`focus-visible:ring-2 focus-visible:ring-primary`).
