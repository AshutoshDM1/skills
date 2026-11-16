# Barrel Re-Export Policy (`index.ts`)

This rule strictly dictates when an AI agent is permitted to create or keep `index.ts` (barrel) files, and where they are forbidden.

---

## The Golden Law of Barrels

```
Does the consumer NEED to import 2 or more peer exports in JSX/code at the same time?
│
├── YES ──► CREATE / KEEP index.ts (Allowed Barrels)
│
└── NO  ──► DO NOT CREATE index.ts (Forbidden Barrels)
            Import directly from the named file.
```

---

## 1. Allowed & Required `index.ts` Files (The "Good" Barrels)

AI agents are permitted to create and maintain `index.ts` files **ONLY** in these 6 designated architectural locations:

| Allowed Location | Purpose | Why It's Good |
| :--- | :--- | :--- |
| **`src/shared/index.ts`** | Public entry point for composite brand blocks | Callers regularly import 3+ brand blocks at once (`import { Section, HeaderText, BrandButton } from "@/shared"`). |
| **`src/modules/[Feature]/Folds/index.ts`** | Aggregates all landing page fold sections | Allows `Home.tsx` to assemble 6–8 page folds in a single import statement (`import { HeroSection, About, Projects } from "./Folds"`). |
| **`src/shared/Icons/index.ts`** | Aggregates custom brand SVG icons | Enables single-line icon imports (`import { GithubIcon, TwitterIcon } from "@/shared/Icons"`). |
| **`src/shared/[CompoundComponent]/index.ts`** | Compound Component public API | Callers must import multiple peer JSX parts (`import { Card, CardHeader, CardBody } from "@/shared/Card"`). |
| **`src/metadata/index.ts`** | Master metadata barrel | Single entry point for `siteConfig`, route metadata, and JSON-LD schemas. |
| **`src/services/index.ts`** | Master API client & domain service barrel | Single entry point for `apiClient`, API services (`emailService`), and types (`ContactEmailPayload`). |

---

## 2. Forbidden & Harmful `index.ts` Files (Strictly Banned)

AI agents must **NEVER** generate `index.ts` files in the following locations:

### ❌ `src/components/ui/index.ts` (The Shadcn Trap)
- **Why Forbidden**: Re-exporting 50+ Shadcn components forces Next.js / Turbopack to evaluate the entire UI library on every route, inflating bundle sizes and slowing Fast Refresh.
- **Enforcement**: Always use direct component imports:
  ```tsx
  import { Button } from "@/components/ui/button";
  import { Dialog } from "@/components/ui/dialog";
  ```

### ❌ `src/modules/[Feature]/index.ts` (Root Feature Index)
- **Why Forbidden**: Creates duplicate `index.ts` tabs in IDEs and obscures the real host file.
- **Enforcement**: Callers import the named host file directly:
  ```tsx
  import { Contact } from "@/modules/Contact/Contact";
  ```

### ❌ `src/modules/[Feature]/Components/index.ts` (Internal Sub-View Barrel)
- **Why Forbidden**: Sub-views inside a feature (e.g. `ContactForm.tsx`) are private implementation details of `Contact.tsx`. An `index.ts` leaks them to the rest of the application.
- **Enforcement**: Host imports directly from the named sub-view:
  ```tsx
  import { ContactForm } from "./Components/ContactForm";
  ```

### ❌ `src/shared/[PropsBasedComponent]/index.ts` (Props-Based Barrel)
- **Why Forbidden**: For autonomous black-box components (`SearchModal`, `BrandButton`), internal sub-views (`SearchTrigger.tsx`, `types.ts`) must remain private.
- **Enforcement**: Direct import:
  ```tsx
  import { SearchModal } from "@/shared/SearchModal/SearchModal";
  ```

### ❌ `src/services/[Domain]/index.ts` (Domain Service Sub-Barrel)
- **Why Forbidden**: Inner service directories (`src/services/Email/`) must never have an `index.ts`. All external callers consume services directly from the master `src/services/index.ts` root barrel.
- **Enforcement**: Export services and types directly in `src/services/index.ts`:
  ```tsx
  import { emailService, type ContactEmailPayload } from "@/services";
  ```

---

## 3. Redundant Barrels to Avoid

Avoid creating nested "middleman" barrels where a parent barrel already exists:
- Avoid `src/metadata/seo/index.ts` and `src/metadata/structuredData/index.ts`. Re-export directly in `src/metadata/index.ts`.
- Avoid `src/hooks/index.ts`. Hooks are usually consumed individually (`import { useMobile } from "@/hooks/use-mobile"`).
