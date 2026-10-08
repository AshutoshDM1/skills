---
name: modular-nextjs-lean
description: Production-grade Lean Modular Architecture for Next.js applications (landing pages, marketing websites, portfolios, legal pages, and content sites). Enforces thin app routers, direct module imports, flat components vs. PascalCase Folds, centralized metadata/SEO, and centralized services without boilerplate.
license: MIT
---

# Lean Modular Next.js Architecture

This skill defines the architecture for building fast, maintainable, and pristine Next.js websites (landing pages, marketing, brand portfolios, and legal pages).

## Core Architectural Principles

1. **Thin App Router Shell (`src/app/`)**: `page.tsx` is strictly an adapter (8–12 lines) that imports the module directly and exports metadata from `@/metadata`.
2. **Direct Module Imports (No Root Index Bloat)**: Callers import directly from `@/modules/[Feature]/[Feature]`. No root `index.ts` files inside module directories.
3. **Flat Components vs. Folds Convention**:
   - **Simple Pages (0–5 components, e.g. Terms, Privacy, Contact)**: Keep `src/modules/[Feature]/components/` flat with `.tsx` files directly inside.
   - **Multi-Section Landing Pages (e.g. Home)**: Use `src/modules/[Feature]/Folds/` (PascalCase, e.g. `Home/Folds/HeroSection/`, `Home/Folds/TechStack/`) to encapsulate major page folds.
4. **Decoupled SEO & Metadata (`src/metadata/`)**: All `Metadata` configurations live in a dedicated layer (`site.config.ts`, `root.ts`, per-page metadata).
5. **Centralized Services (`src/services/`)**: API integrations (e.g. contact forms, email services) are housed in `src/services/` with a shared `apiClient.ts`.
6. **3-Tier UI Hierarchy**:
   - `src/components/ui/` → Headless atomic primitives.
   - `src/shared/` → Composite reusable brand blocks (Navbar, Footer, SectionBadge).
   - `src/modules/[Feature]/` → Feature-specific UI.

---

## Reference Guides

- [Lean Modules & Folds](references/modules.md) — Flat components vs. Folds, direct imports, and thin router integration.
- [Metadata & SEO Architecture](references/metadata.md) — Decoupled metadata configuration (`site.config.ts`, `root.ts`, per-page metadata).
- [Centralized Services](references/services.md) — API callers and network client abstraction.
- [Shared UI & Component Hierarchy](references/shared-ui.md) — 3-tier component hierarchy and shared templates.

---

## Agent Execution Workflows

### Workflow 1: Initializing a Lean Next.js Project Tree

```text
src/
├── app/                  # Next.js App Router (Thin adapters only)
│   ├── layout.tsx
│   └── page.tsx
├── modules/              # PascalCase feature modules
│   └── Home/
│       ├── Folds/        # Major page sections
│       │   ├── HeroSection/
│       │   │   └── HeroSection.tsx
│       │   ├── AboutUs/
│       │   │   └── AboutUs.tsx
│       │   └── index.ts
│       └── Home.tsx      # Assembler importing from './Folds'
├── shared/               # Reusable brand/layout blocks
│   ├── Navbar/
│   ├── Footer/
│   └── index.ts
├── components/           # Base UI primitives & Providers
│   ├── ui/               # Radix / Shadcn primitives
│   └── theme-provider.tsx
├── metadata/             # Decoupled SEO & OpenGraph configurations
│   ├── site.config.ts
│   ├── root.ts
│   ├── home.ts
│   └── index.ts
├── services/             # Centralized API callers & network client
│   ├── apiClient.ts
│   └── index.ts
├── hooks/                # Global utility hooks
├── lib/                  # Generic utilities (e.g. cn helper)
└── types/                # Global TypeScript declarations
```

---

### Workflow 2: Creating a New Page / Feature

1. **Choose Structure**:
   - Simple page (Terms, Privacy, Contact): Create `src/modules/[Feature]/components/` with flat `.tsx` files.
   - Multi-section page: Create `src/modules/[Feature]/Folds/` with fold subfolders and `Folds/index.ts`.
2. **Create Root Module**: `src/modules/[Feature]/[Feature].tsx`.
3. **Create Metadata**: `src/metadata/[feature].ts` and re-export in `src/metadata/index.ts`.
4. **Create Route Adapter**: `src/app/[route]/page.tsx`:
   ```tsx
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

- ❌ **Never** write JSX or inline business logic inside `src/app/**/page.tsx`.
- ❌ **Never** inline `export const metadata: Metadata = { ... }` in `page.tsx` — always import from `@/metadata`.
- ❌ **Never** create root `index.ts` files inside module directories. Import directly from `@/modules/[Feature]/[Feature]`.
- ❌ **Never** create nested subfolders inside `components/` if there are only 0–5 flat components.
- ❌ **Never** cross-import internal subcomponents or folds across modules.
- ✅ **Always** use **`Folds/`** (PascalCase) when designing multi-section landing pages.
- ✅ **Always** use path aliases (`@/modules/*`, `@/shared/*`, `@/metadata/*`, `@/services/*`, `@/components/*`).
