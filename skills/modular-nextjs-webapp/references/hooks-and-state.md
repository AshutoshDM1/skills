# Hooks & State Architecture Guide

This guide defines how state, custom hooks, and async data pipelines are structured in Dynamic Modules.

---

## 1. Local Hooks vs. Global Hooks

- **Local Hooks (`src/modules/[Feature]/hooks/`)**:
  - Encapsulate feature-specific state machines, query fetching (React Query / SWR / Fetch), form handling, and local UI state.
  - Examples: `useBillingHistory.ts`, `useCrmFilters.ts`, `useUserPermissions.ts`.
- **Global Hooks (`src/hooks/`)**:
  - Reusable utility hooks across the entire app.
  - Examples: `useMediaQuery.ts`, `useDebounce.ts`, `useLocalStorage.ts`, `useTheme.ts`.

---

## 2. Dynamic Hook Pattern

```ts
// src/modules/Billing/hooks/useSubscription.ts
import { useState, useEffect, useCallback } from 'react';
import { billingService } from '../services/billing.service';
import type { SubscriptionDetails } from '../types/billing.types';

export function useSubscription() {
  const [subscription, setSubscription] = useState<SubscriptionDetails | null>(null);
  const [isUpdating, setIsUpdating] = useState(false);
  const [isLoading, setIsLoading] = useState(true);

  const fetchSubscription = useCallback(async () => {
    try {
      setIsLoading(true);
      const data = await billingService.getSubscription();
      setSubscription(data);
    } finally {
      setIsLoading(false);
    }
  }, []);

  const upgradePlan = async (planId: string) => {
    try {
      setIsUpdating(true);
      const updated = await billingService.changePlan(planId);
      setSubscription(updated);
    } finally {
      setIsUpdating(false);
    }
  };

  useEffect(() => {
    fetchSubscription();
  }, [fetchSubscription]);

  return {
    subscription,
    isLoading,
    isUpdating,
    upgradePlan,
    refetch: fetchSubscription,
  };
}
```
