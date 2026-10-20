# Centralized Services Architecture Guide (React / Vite)

In React website projects (landing pages, marketing, and legal sites), all network calls, form actions, and API integrations are centralized under `src/services/` using Axios with strict environment validation.

---

## 1. Directory Structure

```text
src/services/
├── apiClient.ts          # Configured Axios instance with request/response interceptors
├── newsletter.service.ts # Newsletter / subscription API caller
├── contact.service.ts    # Contact form API caller
└── index.ts              # Barrel export for all services
```

---

## 2. Strict Environment Client (`src/services/apiClient.ts`)

Fail-fast environment variable validation using Vite's `import.meta.env` with clean Axios error unwrapping:

```ts
import axios from 'axios';

const apiBaseUrl = import.meta.env.VITE_API_BASE_URL;

if (!apiBaseUrl) {
  throw new Error('Missing required environment variable: VITE_API_BASE_URL');
}

export const apiClient = axios.create({
  baseURL: apiBaseUrl,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Response interceptor to unwrap errors
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    const message =
      error.response?.data?.message ||
      error.response?.data?.error ||
      error.message ||
      'An unexpected error occurred';
    return Promise.reject(new Error(message));
  }
);
```

---

## 3. Service Caller Example (`src/services/contact.service.ts`)

```ts
import { apiClient } from './apiClient';

export interface SendContactPayload {
  name: string;
  email: string;
  subject?: string;
  message: string;
}

export interface SendContactResponse {
  success: boolean;
  message: string;
}

export async function sendContactMessage(
  payload: SendContactPayload
): Promise<SendContactResponse> {
  const { data } = await apiClient.post<SendContactResponse>('/api/contact', payload);
  return data;
}
```

---

## 4. Usage in Components

```tsx
import { useState } from 'react';
import { sendContactMessage } from '@/services';

export function ContactForm() {
  const [loading, setLoading] = useState(false);
  const [success, setSuccess] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    setLoading(true);
    setError(null);

    try {
      await sendContactMessage({
        name: 'Jane Doe',
        email: 'jane@example.com',
        message: 'Hello world',
      });
      setSuccess(true);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Submission failed');
    } finally {
      setLoading(false);
    }
  };

  return <form onSubmit={handleSubmit}>...</form>;
}
```
