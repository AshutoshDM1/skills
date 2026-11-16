---
name: better-data-fetching-client
description: Production-grade data-fetching layer with TanStack Query. Covers QueryClient provider configuration, domain-driven custom React hooks in src/hooks/, colocated type contracts, declarative query key factories, and queryClient cache invalidation on mutations.
license: MIT
---

# Data Fetching Architecture with TanStack Query (`src/hooks/`)

This skill governs asynchronous server-state management using **TanStack Query (v5)** in Next.js web applications. It standardizes query provider initialization, colocates hook data contracts, and separates presentation logic from HTTP calls.

---

## 1. Architectural Pipeline

```
┌─────────────────────────────────────────────────────────────┐
│ 1. HTTP Transport Layer (src/services/apiClient.ts)         │
│    Axios instance, fail-fast env check, error unwrap        │
└──────────────────────────────┬──────────────────────────────┘
                               │ consumed by
┌──────────────────────────────▼──────────────────────────────┐
│ 2. Server State Hooks (src/hooks/use<Domain>.ts)            │
│    useQuery, useMutation, type definitions, query keys      │
└──────────────────────────────┬──────────────────────────────┘
                               │ consumed by
┌──────────────────────────────▼──────────────────────────────┐
│ 3. UI Layer (src/modules/<Feature>/)                        │
│    Loading skeletons, optimistic updates, interactive views │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. Core Architectural Laws

### Law 1: Centralized Query Client Setup
Initialize `QueryClient` inside a client component provider (`src/components/Provider.tsx` or similar) using `useState` so each request/session maintains an isolated cache without cross-request leaks.

```tsx
"use client";

import { useState } from "react";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";

export default function AppProvider({ children }: { children: React.ReactNode }) {
  const [queryClient] = useState(
    () =>
      new QueryClient({
        defaultOptions: {
          queries: {
            staleTime: 60 * 1000, // 1 minute fresh window
            refetchOnWindowFocus: false,
          },
        },
      }),
  );

  return <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>;
}
```

---

### Law 2: Domain-Driven Hook Colocation (`src/hooks/`)
Hooks interfacing with APIs belong in `src/hooks/` and follow the `use<Domain>.ts` naming standard (e.g. `useLeads.ts`, `useEmail.ts`).

Each hook file must encapsulate:
1. **TypeScript Contracts**: Payload, response, and filter interfaces.
2. **Query Hooks**: Parameterized queries with deterministic query keys.
3. **Mutation Hooks**: State-modifying actions with explicit `invalidateQueries` calls.

---

### Law 3: Structured Query Keys & Cache Invalidation
Never use unstructured strings for dynamic queries. Query keys must contain all reactive variables:

```typescript
// Deterministic query key containing pagination & filter parameters
queryKey: ['leads', page, limit, status, search]
```

On successful mutations, immediately invalidate relevant cache keys:

```typescript
export function useCreateLeadMutation() {
  const queryClient = useQueryClient();
  return useMutation({
    mutationFn: async (payload: CreateLeadPayload) => {
      const response = await apiClient.post('/leads', payload);
      return response.data;
    },
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['leads'] });
    },
  });
}
```

---

### Law 4: Service Layer Delegation
Hooks should directly consume the central `apiClient` or service methods from `@/services`:

```typescript
import { useMutation } from '@tanstack/react-query';
import { emailService, type ContactEmailPayload } from '@/services';

export function useSendContactEmailMutation() {
  return useMutation({
    mutationFn: (payload: ContactEmailPayload) => emailService.sendContactEmail(payload),
  });
}
```
