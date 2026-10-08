# Centralized Services Architecture Guide

In Lean Next.js projects (landing pages, marketing, and legal sites), all network calls, form actions, and API integrations are centralized under `src/services/` using Axios with strict environment validation.

---

## 1. Directory Structure

```text
src/services/
├── apiClient.ts          # Central Axios client with interceptors & strict env checks
├── Email/
│   ├── email.service.ts  # API functions for sending contact/lead emails
│   ├── email.type.ts     # Input payload and response DTO types
│   └── index.ts          # Service export
└── index.ts              # Root services barrel export
```

---

## 2. API Client Abstraction (`src/services/apiClient.ts`)

Strict environment variable validation and clean Axios instance configuration with response error unwrapping:

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

## 3. Modular Service Pattern

### `src/services/Email/email.type.ts`

```ts
export interface SendEmailPayload {
  name: string;
  email: string;
  subject: string;
  message: string;
}

export interface SendEmailResponse {
  success: boolean;
  messageId?: string;
  error?: string;
}
```

### `src/services/Email/email.service.ts`

```ts
import { apiClient } from '../apiClient';
import type { SendEmailPayload, SendEmailResponse } from './email.type';

export const emailService = {
  sendContactEmail: async (payload: SendEmailPayload): Promise<SendEmailResponse> => {
    const response = await apiClient.post<SendEmailResponse>('/contact', payload);
    return response.data;
  },
};
```

### `src/services/Email/index.ts`

```ts
export * from './email.service';
export * from './email.type';
```

### `src/services/index.ts`

```ts
export * from './apiClient';
export * from './Email';
```

---

## 4. Consuming Services in UI Components & Forms

Because the `apiClient` response interceptor unwraps nested backend errors into a clean, standard JavaScript `Error`, UI components handle loading states, success views, and error banners in a simple **`try / catch / finally`** pattern without repetitive manual error parsing:

```tsx
// Example: src/modules/Contact/components/ContactForm.tsx
'use client';

import { useState } from 'react';
import { useForm } from 'react-hook-form';
import { emailService } from '@/services';
import type { SendEmailPayload } from '@/services';

export function ContactForm() {
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [isSubmitted, setIsSubmitted] = useState(false);
  const [submitError, setSubmitError] = useState<string | null>(null);

  const { register, handleSubmit, reset } = useForm<SendEmailPayload>();

  const onSubmit = async (data: SendEmailPayload) => {
    setIsSubmitting(true);
    setSubmitError(null);
    try {
      await emailService.sendContactEmail(data);
      setIsSubmitted(true);
    } catch (err: unknown) {
      // Interceptor guaranteed that `err` is an Error with a human-readable message
      const msg = err instanceof Error ? err.message : 'Something went wrong. Please try again.';
      setSubmitError(msg);
    } finally {
      setIsSubmitting(false);
    }
  };

  const handleReset = () => {
    setIsSubmitted(false);
    setSubmitError(null);
    reset();
  };

  if (isSubmitted) {
    return (
      <div className="text-center">
        <h3>Message Sent!</h3>
        <p>Thank you for reaching out. We will get back to you shortly.</p>
        <button type="button" onClick={handleReset}>
          Send another note
        </button>
      </div>
    );
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name')} placeholder="Your name" />
      <input {...register('email')} type="email" placeholder="Your email" />
      <textarea {...register('message')} placeholder="Your message" />

      {/* Render error banner if submission fails */}
      {submitError && (
        <div className="rounded-xl border border-red-500/30 bg-red-500/10 p-3 text-xs text-red-400">
          {submitError}
        </div>
      )}

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Sending...' : 'Submit'}
      </button>
    </form>
  );
}
```
