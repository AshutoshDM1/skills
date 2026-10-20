# Dynamic Metadata & SEO Guide

In Dynamic Next.js web applications (dashboards, CRM, portals), metadata must support both static SEO routes and dynamic authenticated views.

---

## 1. Decoupled Structure

```text
src/metadata/
├── site.config.ts        # App constants
├── root.ts               # Root layout metadata
├── dashboard.ts          # Static dashboard metadata
├── settings.ts           # Settings metadata
└── index.ts              # Central barrel export
```

---

## 2. Static Dashboard Metadata (`src/metadata/dashboard.ts`)

```ts
import type { Metadata } from 'next';
import { siteConfig } from './site.config';

export const dashboardMetadata: Metadata = {
  title: 'Dashboard Overview',
  description: `Manage your analytics and team performance on ${siteConfig.name}.`,
  robots: {
    index: false,
    follow: false,
  },
};
```

---

## 3. Dynamic Metadata in App Router

When a page requires runtime params (e.g. `src/app/(dashboard)/projects/[id]/page.tsx`), use Next.js `generateMetadata`:

```tsx
import type { Metadata } from 'next';
import ProjectView from '@/modules/ProjectView/ProjectView';

interface Props {
  params: Promise<{ id: string }>;
}

export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const { id } = await params;
  return {
    title: `Project #${id}`,
    description: `Project details for ${id}`,
  };
}

export default async function Page({ params }: Props) {
  const { id } = await params;
  return <ProjectView projectId={id} />;
}
```
