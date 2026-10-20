# Dynamic Modules Architecture Guide

This guide defines the standards for building **Dynamic Modules** in Next.js applications (ideal for SaaS products, web apps, CRMs, interactive dashboards, and client portals).

---

## 1. Core Philosophy: The Vertical Slice Blueprint

Dynamic modules represent full-featured, stateful, and interactive domains. Instead of scattering logic across top-level folders, each dynamic module colocates its complete vertical slice:

```text
src/modules/Dashboard/
├── components/           # Feature UI components
│   ├── StatCard.tsx
│   ├── AnalyticsChart.tsx
│   └── ActivityFeed.tsx
├── hooks/                # Feature-specific state, queries, & side-effects
│   ├── useDashboardMetrics.ts
│   └── useActivityStream.ts
├── services/             # Feature-specific API endpoints & mutations
│   └── dashboard.service.ts
├── types/                # Feature DTOs, schemas, & TypeScript interfaces
│   └── dashboard.types.ts
└── Dashboard.tsx         # Root feature assembler & state orchestrator
```

---

## 2. Module Internal Communication

### Local Types (`types/dashboard.types.ts`)

```ts
export interface DashboardMetric {
  id: string;
  label: string;
  value: number;
  change: number;
}

export interface ActivityItem {
  id: string;
  user: string;
  action: string;
  timestamp: string;
}

export interface DashboardResponse {
  metrics: DashboardMetric[];
  activities: ActivityItem[];
}
```

### Local Service (`services/dashboard.service.ts`)

```ts
import { apiClient } from '@/services/apiClient';
import type { DashboardResponse } from '../types/dashboard.types';

export const dashboardService = {
  getOverview: async (): Promise<DashboardResponse> => {
    return apiClient.get<DashboardResponse>('/api/dashboard/overview');
  },
};
```

### Local Hook (`hooks/useDashboardMetrics.ts`)

```ts
import { useState, useEffect } from 'react';
import { dashboardService } from '../services/dashboard.service';
import type { DashboardMetric } from '../types/dashboard.types';

export function useDashboardMetrics() {
  const [metrics, setMetrics] = useState<DashboardMetric[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    dashboardService
      .getOverview()
      .then((data) => setMetrics(data.metrics))
      .catch((err) => setError(err.message))
      .finally(() => setIsLoading(false));
  }, []);

  return { metrics, isLoading, error };
}
```

### Root Feature Assembler (`Dashboard.tsx`)

```tsx
'use client';

import { useDashboardMetrics } from './hooks/useDashboardMetrics';
import { StatCard } from './components/StatCard';
import { AnalyticsChart } from './components/AnalyticsChart';

export function Dashboard() {
  const { metrics, isLoading, error } = useDashboardMetrics();

  if (isLoading) return <div className="p-8">Loading dashboard...</div>;
  if (error) return <div className="p-8 text-destructive">Error: {error}</div>;

  return (
    <div className="flex flex-col gap-8 p-6 lg:p-10 max-w-7xl mx-auto w-full">
      <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
        {metrics.map((metric) => (
          <StatCard key={metric.id} metric={metric} />
        ))}
      </div>
      <AnalyticsChart />
    </div>
  );
}

export default Dashboard;
```

---

## 3. Thin App Router Adapter

```tsx
// src/app/(dashboard)/dashboard/page.tsx
import type { Metadata } from 'next';
import Dashboard from '@/modules/Dashboard/Dashboard';
import { dashboardMetadata } from '@/metadata';

export const metadata: Metadata = dashboardMetadata;

export default function DashboardPage() {
  return <Dashboard />;
}
```

---

## 4. Architectural Rules for Dynamic Modules

1. **Local Colocation**: If a hook, service, or type is only used within `src/modules/Dashboard/`, it **must** remain inside that module.
2. **Direct Imports**: Routes import directly via `@/modules/Dashboard/Dashboard`.
3. **Never Cross-Import**: A different module (e.g. `src/modules/Settings/`) must **never** reach into `src/modules/Dashboard/services/`. Shared services must live in root `src/services/`.
