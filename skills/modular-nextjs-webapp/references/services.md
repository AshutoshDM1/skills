# Dynamic Services & API Architecture Guide

This guide defines how services, data fetching, mutations, and API clients operate in Dynamic Next.js applications using Axios and strict environment validation.

---

## 1. Global Services vs. Module-Local Services

| Type                | Location                          | Purpose                                                                                                             |
| :------------------ | :-------------------------------- | :------------------------------------------------------------------------------------------------------------------ |
| **Global Services** | `src/services/`                   | Universal endpoints used across multiple modules (Auth, User Session, Global Billing, File Uploads, Notifications). |
| **Module Services** | `src/modules/[Feature]/services/` | Endpoints specific only to that business domain (e.g. `src/modules/Analytics/services/analytics.service.ts`).       |

---

## 2. API Client Wrapper (`src/services/apiClient.ts`)

```ts
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

// Automatic error extraction middleware
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

---

## 3. Module Service Example

```ts
// src/modules/Dashboard/services/dashboard.service.ts
import { apiClient } from '@/services/apiClient';
import type { DashboardResponse } from '../types/dashboard.types';

export const dashboardService = {
  getOverview: async (): Promise<DashboardResponse> => {
    const response = await apiClient.get<DashboardResponse>('/dashboard/overview');
    return response.data;
  },
};
```

---

## 4. Consuming Services in UI Components & Forms

When submitting forms or executing asynchronous mutations, use the standard `try / catch / finally` pattern. The `apiClient` interceptor guarantees that `err.message` contains a clean, human-readable error string:

```tsx
'use client';

import { useState } from 'react';
import { dashboardService } from '../services/dashboard.service';

export function ActionButton() {
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const handleAction = async () => {
    setIsSubmitting(true);
    setError(null);
    try {
      await dashboardService.getOverview();
    } catch (err: unknown) {
      const msg = err instanceof Error ? err.message : 'Action failed';
      setError(msg);
    } finally {
      setIsSubmitting(false);
    }
  };

  return (
    <div>
      {error && <p className="text-red-500 text-xs">{error}</p>}
      <button onClick={handleAction} disabled={isSubmitting}>
        {isSubmitting ? 'Loading...' : 'Trigger Action'}
      </button>
    </div>
  );
}
```
