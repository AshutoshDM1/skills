---
name: better-api-service-client
description: Production-grade API service layer architecture for Next.js and React applications. Enforces centralized Axios configuration in apiClient.ts, domain-driven PascalCase subdirectories in src/services/, strict payload and response typing (*.type.ts), dedicated service methods (*.service.ts), and a single root barrel export (src/services/index.ts) with zero inner barrel sprawl.
license: MIT
---

# API Service Client Architecture (`src/services/`)

This skill governs the structure, client setup, and domain organization of API services in `src/services/`. It eliminates ad-hoc fetch calls, enforces centralized error normalization, and mandates clean domain encapsulation.

---

## 1. Directory Blueprint

```
src/services/
├── apiClient.ts          # Central Axios instance & error interceptor
├── index.ts              # ONLY allowed barrel re-exporting client & domain APIs
└── Email/                # PascalCase domain directory
    ├── email.service.ts  # HTTP endpoint methods
    └── email.type.ts     # Request payload and response interfaces
```

---

## 2. Core Architectural Laws

### Law 1: Centralized Fail-Fast Client (`apiClient.ts`)
The `apiClient.ts` file acts as the primary transport engine. It must:
- Validate `NEXT_PUBLIC_API_URL` immediately at runtime without silent fallbacks.
- Configure standardized `baseURL` (`${ApiBackend}/api/v1`) and default headers.
- Intercept HTTP errors to unwrap backend message payloads into clean Error instances.

```typescript
import axios from 'axios';

export const ApiBackend = process.env.NEXT_PUBLIC_API_URL;

if (!ApiBackend) {
  throw new Error('Missing required environment variable: NEXT_PUBLIC_API_URL');
}

export const apiClient = axios.create({
  baseURL: `${ApiBackend}/api/v1`,
  headers: {
    'Content-Type': 'application/json',
  },
});

apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    const message =
      error?.response?.data?.error ||
      error?.response?.data?.message ||
      error?.message ||
      'An unexpected error occurred';
    return Promise.reject(new Error(message));
  },
);
```

### Law 2: PascalCase Domain Folders
All API sub-domains inside `src/services/` must use **PascalCase**:
- ✅ `src/services/Email/`
- ✅ `src/services/Auth/`
- ✅ `src/services/User/`
- ❌ `src/services/email/`
- ❌ `src/services/auth-service/`

### Law 3: Dual-File Domain Separation
Each API domain folder contains exactly two dedicated files:
1. `[domain].type.ts`: Defines request payloads and API response interfaces.
2. `[domain].service.ts`: Implements endpoint methods invoking `apiClient`.

Example `email.type.ts`:
```typescript
export interface ContactEmailPayload {
  fullName: string;
  email: string;
  message: string;
}

export interface ContactEmailResponse {
  success: boolean;
  message: string;
}
```

Example `email.service.ts`:
```typescript
import { apiClient } from '../apiClient';
import type { ContactEmailPayload, ContactEmailResponse } from './email.type';

export const emailService = {
  sendContactEmail: async (payload: ContactEmailPayload): Promise<ContactEmailResponse> => {
    const response = await apiClient.post<ContactEmailResponse>('/email/contact', payload);
    return response.data;
  },
};
```

### Law 4: The Single Root Barrel Rule
- ✅ **ALLOWED**: `src/services/index.ts` is the single public entry point re-exporting the client, domain services, and types.
- ❌ **STRICTLY BANNED**: `src/services/[Domain]/index.ts`. Domain subdirectories must never contain an `index.ts`.

Example `src/services/index.ts`:
```typescript
export { apiClient, ApiBackend } from './apiClient';
export { emailService } from './Email/email.service';
export type { ContactEmailPayload, ContactEmailResponse } from './Email/email.type';
```

---

## 3. Scaffolding a New Domain Service

When adding a new API domain (e.g. `Auth`):
1. Create `src/services/Auth/auth.type.ts` with typed interfaces.
2. Create `src/services/Auth/auth.service.ts` using `apiClient`.
3. Do **NOT** create `src/services/Auth/index.ts`.
4. Re-export public members from `src/services/index.ts`.
