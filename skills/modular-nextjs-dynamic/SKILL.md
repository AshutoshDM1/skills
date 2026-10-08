---
name: modular-nextjs-dynamic
description: Production-grade Dynamic Modular Architecture for Next.js applications (SaaS web apps, CRMs, interactive dashboards, customer portals, and data-heavy tools). Enforces thin app routers, vertical slice module encapsulation with colocated components, hooks, services, and types.
license: MIT
---

# Dynamic Modular Next.js Architecture

This skill defines the architecture for building complex, interactive, and data-driven Next.js web applications (SaaS, CRMs, internal tools, and dashboards).

## Core Architectural Principles

1. **Thin App Router Shell (`src/app/`)**: `page.tsx` is strictly an adapter (8–12 lines) that renders the root dynamic module and exports metadata.
2. **Vertical Slice Domain Encapsulation (`src/modules/[Feature]/`)**: Each dynamic feature encapsulates its own complete stack:
   - `components/` → Feature-specific UI.
   - `hooks/` → Feature state machines, query hooks, and async handlers.
   - `services/` → Feature-specific API endpoints and mutation functions.
   - `types/` → Feature TypeScript interfaces, DTOs, and schemas.
   - `[Feature].tsx` → Root state and view orchestrator.
3. **Direct Module Imports (No Root Index Bloat)**: Routes import directly from `@/modules/[Feature]/[Feature]`.
4. **Separation of Global vs. Local Services**:
   - `src/services/` → Universal cross-cutting services (Auth, Session, Global Billing).
   - `src/modules/[Feature]/services/` → Domain-isolated endpoints.
5. **Decoupled Metadata Layer (`src/metadata/`)**: Centralized static and dynamic metadata configs.

---

## Reference Guides

- [Dynamic Modules Architecture](references/modules.md) — Vertical slice structure, local communication, and thin router integration.
- [Services & API Layer](references/services.md) — API client abstraction, local vs. global endpoints.
- [Hooks & State Management](references/hooks-and-state.md) — Colocated custom hooks, async data fetching, and state machines.
- [Dynamic Metadata](references/metadata.md) — Metadata for authenticated views and dynamic route parameters.

---

## Agent Execution Workflows

### Workflow 1: Initializing a Dynamic Next.js Project Tree

```text
src/
├── app/                  # Next.js App Router (Thin wrappers & route groups)
│   ├── (auth)/
│   ├── (dashboard)/
│   │   └── dashboard/
│   │       └── page.tsx
│   ├── layout.tsx
│   └── page.tsx
├── modules/              # Vertical Slice Dynamic Modules
│   └── Dashboard/
│       ├── components/   # Feature UI
│       │   ├── StatCard.tsx
│       │   └── AnalyticsChart.tsx
│       ├── hooks/        # Local state & queries
│       │   └── useDashboardMetrics.ts
│       ├── services/     # Local API calls
│       │   └── dashboard.service.ts
│       ├── types/        # Local DTOs & types
│       │   └── dashboard.types.ts
│       └── Dashboard.tsx # Root orchestrator
├── shared/               # Reusable composite application components
│   ├── AppHeader/
│   ├── Sidebar/
│   └── index.ts
├── components/           # Base UI primitives & Providers
│   ├── ui/               # Radix / Shadcn primitives
│   └── theme-provider.tsx
├── metadata/             # Decoupled SEO & route metadata
├── services/             # Global API services & network client
│   ├── apiClient.ts
│   └── index.ts
├── hooks/                # Universal utility hooks
├── lib/                  # Generic utilities (e.g. cn helper)
└── types/                # Global TypeScript declarations
```

---

### Workflow 2: Creating a New Dynamic Feature

1. **Create Module Folder**: `src/modules/[FeatureName]/`
2. **Define Local Types**: `src/modules/[FeatureName]/types/[featureName].types.ts`
3. **Define Local Services**: `src/modules/[FeatureName]/services/[featureName].service.ts` using `apiClient`.
4. **Create Custom Hook**: `src/modules/[FeatureName]/hooks/use[FeatureName].ts` handling state & fetching.
5. **Create Feature UI**: `src/modules/[FeatureName]/components/`
6. **Assemble Root Module**: `src/modules/[FeatureName]/[FeatureName].tsx`
7. **Connect to App Router**:
   ```tsx
   // src/app/(dashboard)/[route]/page.tsx
   import type { Metadata } from 'next';
   import FeatureName from '@/modules/FeatureName/FeatureName';
   import { featureNameMetadata } from '@/metadata';

   export const metadata: Metadata = featureNameMetadata;

   export default function Page() {
     return <FeatureName />;
   }
   ```

---

## Strict Rules & Constraints

- ❌ **Never** write JSX, useEffects, or query fetching inside `src/app/**/page.tsx`.
- ❌ **Never** place feature-specific hooks/services in global `src/hooks/` or `src/services/` — keep them colocated in `src/modules/[Feature]/`.
- ❌ **Never** cross-import internal hooks/services from another module. If shared, promote to global `src/services/` or `src/hooks/`.
- ❌ **Never** create root `index.ts` files inside modules. Always import `@/modules/[Feature]/[Feature]`.
