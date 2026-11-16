---
name: production-website-codebase
description: Master orchestrator skill that audits, scaffolds, and modernizes an entire Next.js website codebase by sequentially executing the 7 core architecture skills (better-modern-frontend-stack, better-api-service-client, better-data-fetching-client, better-modules-folder-structure, better-shared-folder-structure, better-component-architecture, better-modular-seo-structure).
license: MIT
---

# Production Website Codebase Blueprint

This skill is the **master orchestrator** for scaffolding, auditing, or refactoring a modern, production-grade Next.js **website codebase**.

It coordinates and executes the 7 fundamental architectural pillars sequentially:

```
┌─────────────────────────────────────────────────────────────┐
│ 1. BASE STACK AUDIT (better-modern-frontend-stack)          │
│    Shadcn pre-install, Hugeicons, motion, Zustand, Query    │
└──────────────────────────────┬──────────────────────────────┘
                               │ feeds into
┌──────────────────────────────▼──────────────────────────────┐
│ 2. API SERVICE CLIENT (better-api-service-client)           │
│    Fail-fast apiClient.ts & PascalCase src/services/        │
└──────────────────────────────┬──────────────────────────────┘
                               │ powers
┌──────────────────────────────▼──────────────────────────────┐
│ 3. DATA FETCHING CLIENT (better-data-fetching-client)       │
│    QueryClientProvider & domain hooks in src/hooks/         │
└──────────────────────────────┬──────────────────────────────┘
                               │ supplies
┌──────────────────────────────▼──────────────────────────────┐
│ 4. MODULAR APP ROUTING (better-modules-folder-structure)    │
│    Thin 8-line app/ adapters & PascalCase src/modules/      │
└──────────────────────────────┬──────────────────────────────┘
                               │ integrates with
┌──────────────────────────────▼──────────────────────────────┐
│ 5. SHARED BRAND CORE (better-shared-folder-structure)       │
│    src/shared/ with the 8 universal starter blocks          │
└──────────────────────────────┬──────────────────────────────┘
                               │ refines
┌──────────────────────────────▼──────────────────────────────┐
│ 6. COMPONENT ARCHITECTURE (better-component-architecture)   │
│    Props-Based vs. Compound & strict line-count thresholds  │
└──────────────────────────────┬──────────────────────────────┘
                               │ seals with
┌──────────────────────────────▼──────────────────────────────┐
│ 7. MODULAR SEO & SCHEMAS (better-modular-seo-structure)     │
│    src/metadata/ site.config.ts, route SEO & JSON-LD        │
└─────────────────────────────────────────────────────────────┘
```

---

## The 7-Phase Execution Pipeline

When this skill is invoked, the agent must guide the user through each phase step by step, **prompting the user before making major structural or file-system alterations**:

---

### Phase 1: Base Stack Audit & Scaffolding (`better-modern-frontend-stack`)

1. **Scan `package.json` and `components.json`**:
   - Check UI library: Is `shadcn` installed, and are components pre-installed in `@/components/ui`?
   - Check icons: Is `hugeicons-react` installed (or overused `lucide-react`)?
   - Check animation: Is modern `motion` installed (or legacy `framer-motion`)?
   - Check state: Is `zustand` installed (or boomer Redux/Context abuse)?
   - Check server state: Is `@tanstack/react-query` + `axios` configured?
2. **Execute Phase Action**:
   - **If modern setup is complete**: Output a modernity score (e.g., `9.8/10 ⚡ Anti-Slop Certified`), praise the setup, and **make zero code changes**.
   - **If fresh/missing**: Prompt the user:
     > *"No modern base stack detected. Would you like me to scaffold Shadcn + Hugeicons + Motion + Zustand + TanStack Query? [Yes/No]"*
   - **If half-baked**: Diagnose legacy libraries and provide exact upgrade commands (`pnpm add motion zustand ...`).

---

### Phase 2: API Service Layer (`better-api-service-client`)

1. **Scan `src/services/`**:
   - Check `src/services/apiClient.ts` for fail-fast `NEXT_PUBLIC_API_URL` assertions and error response unwrapping.
   - Verify that domain folders are PascalCase (e.g. `src/services/Email/`).
   - Check that domain folders contain only `[domain].service.ts` and `[domain].type.ts` (zero sub-barrel `index.ts` files).
   - Ensure the root `src/services/index.ts` is the single public export point.
2. **Execute Phase Action**:
   - Scaffold missing `apiClient.ts`, root barrel, or domain service pairs as needed.

---

### Phase 3: Data Fetching Layer (`better-data-fetching-client`)

1. **Verify Query Client Setup**:
   - Ensure `QueryClientProvider` is configured in `src/components/Provider.tsx` with request-safe `useState` instancing and sensible default `staleTime`.
2. **Verify Custom Domain Hooks (`src/hooks/`)**:
   - Check for clean custom hooks (`useLeads.ts`, `useEmail.ts`) orchestrating `useQuery`, `useMutation`, and cache invalidation via `useQueryClient`.
   - Ensure hooks consume typed methods from `@/services`.

---

### Phase 4: Modular Route Architecture (`better-modules-folder-structure`)

1. **Scan `src/app/` and existing pages**:
   - Check if `page.tsx` files contain 100+ lines of inline JSX (anti-pattern).
   - Check if routes map cleanly to `src/modules/[Feature]/[Feature].tsx`.
2. **Execute Phase Action**:
   - For multi-section landing pages (e.g. `Home`): Scaffold or refactor into the **`Folds/` pattern** (`Folds/index.ts`, `HeroSection/`, `About/`).
   - For simple utility pages (e.g. `Contact`): Scaffold or refactor into a flat **`Components/` subfolder** without barrel files.
   - Convert all `src/app/**/page.tsx` files into thin 8–12 line route adapters:
     ```tsx
     import { Home } from "@/modules/Home/Home";
     export default function Page() { return <Home />; }
     ```

---

### Phase 5: Shared Brand Component System (`better-shared-folder-structure`)

1. **Verify `src/shared/`**:
   - Check for the **8 Universal Starter Blocks**:
     1. `Section` (enforcing `max-w-7xl mx-auto px-4 sm:px-6 py-16 md:py-24`)
     2. `BrandButton` (high-polish interactive CTA button)
     3. `HeaderText` (standardized responsive heading hierarchy)
     4. `DescriptionText` (muted readable sub-headline)
     5. `Loader` (universal brand spinner)
     6. `ErrorPage` (404/500 fallback boundary)
     7. `Breadcrumb` (sub-route navigation)
     8. `Icons` (custom brand SVGs for GitHub, Twitter, Discord)
2. **Wire Master Entry Point**:
   - Ensure `src/shared/index.ts` re-exports all public shared blocks.
3. **Integrate into Modules**:
   - Replace raw repetitive `<div>` wrappers and ad-hoc buttons across `src/modules/` with the standardized shared components.

---

### Phase 6: Component Architecture & Refactoring (`better-component-architecture`)

1. **Classify Custom Components**:
   - Enforce the **80–90% Props-Based rule**: Keep components as black boxes taking typed props (`<ProjectCard ... />`).
   - Limit **Compound Components** (10%) strictly to flexible JSX containers (`Tabs`, `Accordion`, `Dialog`).
2. **Enforce Line-Count Thresholds**:
   - `< 300 lines`: Keep in a **Single File** (`Card/Card.tsx` or `BrandButton/BrandButton.tsx`) to avoid micro-file sprawl.
   - `> 300–500 lines`: Decompose internal sub-views into private colocated files.
3. **Enforce the Barrel Law**:
   - 🛑 **Strictly ban `index.ts`** in props-based component folders and feature `Components/` directories.
   - 💡 **Keep `index.ts`** strictly for compound components where callers import 2+ JSX pieces.

---

### Phase 7: Modular SEO & Metadata Architecture (`better-modular-seo-structure`)

1. **Verify `src/metadata/`**:
   - `site.config.ts`: Assert that `NEXT_PUBLIC_SITE_URL` exists at startup (zero silent fallbacks).
   - `seo/`: Create per-route metadata objects (`root.ts`, `home.ts`) enforcing `alternates.canonical` and `1200x630` social cards.
   - `structuredData/`: Create Schema.org JSON-LD definitions (`organization.ts`, `website.ts`) for Google rich snippets.
   - `index.ts`: Master barrel exporting configs, SEO, and structured data.
2. **Bind to Route Adapters**:
   - In `src/app/page.tsx`, export `metadata` and inject JSON-LD script tags alongside `<Home />`.

---

## Agent Invocation Decision Matrix

When asked to run the master blueprint:
1. **Fresh Starter (Empty / Basic Next.js)**: Automatically execute Phases 1 through 7 in order, creating a complete production-ready scaffold with API client, TanStack Query hooks, sample Home, Contact, Shared Blocks, and SEO.
2. **Existing Project**:
   - Run a non-destructive audit first across all 7 phases.
   - Present a comprehensive **Health & Modernity Report**.
   - Ask the user which specific phase(s) they would like to execute or refactor first.
