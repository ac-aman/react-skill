# Clean Architecture (Hexagonal / DDD in React)

Clean Architecture (adapted from Robert C. Martin / Uncle Bob) isolates **core business logic and domain entities** from external UI frameworks (React), state containers, and HTTP libraries.

This architecture pattern is best suited for **complex enterprise React applications, heavy client-side applications (offline-first apps, canvas editors, financial tools), or multi-platform React/React Native projects sharing domain logic**.

---

## 💡 Core Principles & Layer Boundaries

```text
  [ Frameworks & Drivers (React UI, Axios, LocalStorage, Router) ]
                              │
                              ▼
        [ Interface Adapters (Presenters, Controllers) ]
                              │
                              ▼
           [ Use Cases / Application Business Rules ]
                              │
                              ▼
           [ Enterprise Domain Entities & Rules ]
```

1. **Dependency Rule**: Dependencies point strictly **inward**. Domain Entities and Use Cases must NEVER depend on React, JSX, Axios, Next.js, or browser APIs.
2. **Framework Decoupling**: If React is replaced tomorrow with Svelte or Vue, 100% of domain entities and use cases remain unmodified.
3. **Inversion of Control (Ports & Adapters)**: Define TypeScript interfaces (Ports) for external repositories or services. Real adapters (e.g. `FetchUserRepository`, `LocalStorageAuth`) implement these interfaces and are injected at runtime.

---

## 📁 Recommended Directory Structure

```text
src/
├── domain/                     # 🟢 LAYER 1: Core Domain Entities & Rules (Pure TypeScript)
│   ├── entities/               # Domain Models & Business Logic
│   │   ├── User.ts             # User domain entity validation & business methods
│   │   ├── Order.ts
│   │   └── Money.ts            # Value object for currency math
│   └── errors/                 # Domain-specific error definitions
│       └── InsufficientFundsError.ts
│
├── use-cases/                  # 🟡 LAYER 2: Application Use Cases (Orchestration)
│   ├── ports/                  # Interfaces (Contracts) for external drivers
│   │   ├── UserRepositoryPort.ts
│   │   ├── PaymentGatewayPort.ts
│   │   └── AnalyticsPort.ts
│   ├── authenticate-user.ts    # Use case function or class
│   └── checkout-order.ts       # Use case orchestrating domain entities
│
├── adapters/                   # 🔴 LAYER 3: Interface Adapters & Presenters
│   ├── repositories/           # Implementations of Ports using real APIs
│   │   ├── HttpUserRepository.ts
│   │   └── LocalStorageAuth.ts
│   ├── presenters/             # Formatter functions converting domain objects to UI View Models
│   │   └── user-presenter.ts
│   └── view-models/            # UI Data Structures
│
├── infrastructure/             # 🟣 LAYER 4: Frameworks, React UI & Drivers
│   ├── react/                  # React Framework & UI Components
│   │   ├── components/         # Presentational JSX components
│   │   ├── hooks/              # Custom React hooks wrapping use-cases via Dependency Injection
│   │   │   └── use-checkout.ts
│   │   └── context/            # Dependency Injection Provider Context
│   │       └── DependencyContext.tsx
│   ├── http/                   # Axios / Fetch client wrapper
│   └── storage/                # Web Storage wrappers
│
└── main.tsx                    # Application Wireup / Entry Point
```

---

## 🛡️ Code Implementation Pattern

### 1. Pure Domain Entity (`domain/entities/User.ts`)
```typescript
// Zero React or external library imports!
export interface UserProps {
  id: string;
  email: string;
  role: 'admin' | 'user';
  isVerified: boolean;
}

export class User {
  constructor(private readonly props: UserProps) {}

  get id(): string { return this.props.id; }
  get email(): string { return this.props.email; }
  get isAdmin(): boolean { return this.props.role === 'admin'; }

  canAccessAdminPanel(): boolean {
    return this.isAdmin && this.props.isVerified;
  }
}
```

### 2. Port Interface (`use-cases/ports/UserRepositoryPort.ts`)
```typescript
import { User } from '../../domain/entities/User';

export interface UserRepositoryPort {
  getById(id: string): Promise<User | null>;
  save(user: User): Promise<void>;
}
```

### 3. Use Case (`use-cases/verify-user.ts`)
```typescript
import { UserRepositoryPort } from './ports/UserRepositoryPort';

export class VerifyUserUseCase {
  constructor(private userRepository: UserRepositoryPort) {}

  async execute(userId: string): Promise<void> {
    const user = await this.userRepository.getById(userId);
    if (!user) throw new Error('User not found');
    
    // Business logic execution
    // Save updated state back through port
  }
}
```

### 4. React Hook Integration (`infrastructure/react/hooks/use-verify-user.ts`)
```typescript
import { useState } from 'react';
import { useDependencies } from '../context/DependencyContext';

export function useVerifyUser() {
  const { verifyUserUseCase } = useDependencies(); // Injected use case
  const [loading, setLoading] = useState(false);

  const verify = async (userId: string) => {
    setLoading(true);
    try {
      await verifyUserUseCase.execute(userId);
    } finally {
      setLoading(false);
    }
  };

  return { verify, loading };
}
```

---

## ✅ Best Practices Checklist

- [ ] Does `domain/` contain 0 imports from `react`, `react-dom`, or external HTTP libraries?
- [ ] Are external infrastructure adapters decoupled via TypeScript interfaces (Ports)?
- [ ] Are use-cases easily unit testable without mocking React components or rendering DOM nodes?
