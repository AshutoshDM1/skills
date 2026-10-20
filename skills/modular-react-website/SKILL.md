---
name: modular-react-website
description: Production-grade Website Modular Architecture for React & Vite applications (landing pages, marketing websites, brand portfolios, legal pages, and content sites). Enforces thin route adapters, direct module imports, flat components vs. PascalCase Folds, centralized metadata/SEO, and centralized services without boilerplate.
license: MIT
---

# Modular React Website Architecture

This skill defines the architecture for building fast, maintainable, and pristine React (Vite / SPA) websites (landing pages, marketing, brand portfolios, and legal pages).

## Core Architectural Principles

1. **Thin Route Adapter Shell (`src/routes/` or `src/router.tsx`)**: Route files are strictly thin adapters (8–15 lines) that import the module directly and render the page head metadata.
2. **Direct Module Imports (No Root Index Bloat)**: Callers import directly from `@/modules/[Feature]/[Feature]`. No root `index.ts` files inside module directories.
3. **Flat Components vs. Folds Convention**:
   - **Simple Pages (0–5 components, e.g. Terms, Privacy, Contact)**: Keep `src/modules/[Feature]/components/` flat with `.tsx` files directly inside.
   - **Multi-Section Landing Pages (e.g. Home)**: Use `src/modules/[Feature]/Folds/` (PascalCase, e.g. `Home/Folds/HeroSection/`, `Home/Folds/TechStack/`) to encapsulate major page folds.
4. **Decoupled SEO & Head Metadata (`src/metadata/`)**: All SEO configurations, titles, OpenGraph tags, and Schema.org data live in a dedicated layer (`site.config.ts`, per-page metadata) consumed via a shared `PageHead` component or helmet.
5. **Centralized Services (`src/services/`)**: API integrations (e.g. contact forms, email services) are housed in `src/services/` with a shared `apiClient.ts` using Vite environment variables (`import.meta.env.VITE_*`).
6. **3-Tier UI Hierarchy**:
   - `src/components/ui/` → Headless atomic primitives (Radix, Shadcn).
   - `src/shared/` → Composite reusable brand blocks (Navbar, Footer, SectionBadge, PageHead).
   - `src/modules/[Feature]/` → Feature-specific UI.

---

## Reference Guides

- [Website Modules & Folds](references/modules.md) — Flat components vs. Folds, direct imports, and thin router integration.
- [SEO & Metadata Architecture](references/metadata.md) — Decoupled metadata configuration (`site.config.ts`, `PageHead`, per-page metadata).
- [Centralized Services](references/services.md) — API callers and network client abstraction (`import.meta.env`).
- [Shared UI & Component Hierarchy](references/shared-ui.md) — 3-tier component hierarchy and shared layout blocks.

---

## Agent Execution Workflows

### Workflow 1: Initializing a React Website Project Tree

```text
src/
├── routes/               # Router definitions / Thin route adapters
│   ├── HomeRoute.tsx
│   ├── ContactRoute.tsx
│   └── router.tsx        # createBrowserRouter or router config
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
│   ├── PageHead/         # SEO head adapter component
│   └── index.ts
├── components/           # Base UI primitives & Providers
│   ├── ui/               # Radix / Shadcn primitives
│   └── theme-provider.tsx
├── metadata/             # Decoupled SEO & OpenGraph configurations
│   ├── site.config.ts
│   ├── types.ts          # PageMetadata interface
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
3. **Create Metadata**: `src/metadata/seo/[feature].ts` and re-export in `src/metadata/index.ts`.
4. **Create Route Adapter**: `src/routes/[Feature]Route.tsx`:
   ```tsx
   import FeatureName from '@/modules/FeatureName/FeatureName';
   import { PageHead } from '@/shared/PageHead';
   import { featureNameMetadata } from '@/metadata';

   export default function FeatureNameRoute() {
     return (
       <>
         <PageHead {...featureNameMetadata} />
         <FeatureName />
       </>
     );
   }
   ```

---

## Strict Rules & Constraints

- ❌ **Never** write JSX or inline business logic inside `src/routes/**` adapters.
- ❌ **Never** hardcode titles or SEO meta tags directly in components — always import from `@/metadata`.
- ❌ **Never** create root `index.ts` files inside module directories. Import directly from `@/modules/[Feature]/[Feature]`.
- ❌ **Never** create nested subfolders inside `components/` if there are only 0–5 flat components.
- ❌ **Never** cross-import internal subcomponents or folds across modules.
- ❌ **Never** use `process.env` in Vite applications — use `import.meta.env.VITE_*` with strict fail-fast validation.
- ✅ **Always** use **`Folds/`** (PascalCase) when designing multi-section landing pages.
- ✅ **Always** use path aliases (`@/modules/*`, `@/shared/*`, `@/metadata/*`, `@/services/*`, `@/components/*`).
