---
name: better-modules-folder-structure
description: Production-grade Modular Architecture for Next.js web applications. Enforces thin App Router adapters, PascalCase feature modules in src/modules/, flat Components/ for simple pages, and Folds/ architecture for complex landing pages.
license: MIT
---

# Modular Next.js Website Architecture

This skill governs the **modular feature architecture** of Next.js web applications. It enforces clean separation of concerns by keeping `src/app/` strictly as thin route adapters and encapsulating all page UI, state, and sections inside `src/modules/`.

---

## 1. Core Architectural Laws

1. **PascalCase Feature Folders**: Every feature directory in `src/modules/` is strictly **PascalCase** (e.g. `src/modules/Home/`, `src/modules/Contact/`, `src/modules/Blog/`).
2. **1:1 Route Mapping**: Every route in Next.js App Router maps directly to a feature module:
   - `/` $\rightarrow$ `src/modules/Home/Home.tsx`
   - `/contact` $\rightarrow$ `src/modules/Contact/Contact.tsx`
   - `/blog` $\rightarrow$ `src/modules/Blog/Blog.tsx`
3. **Thin App Router Adapters**: `src/app/**/page.tsx` must never exceed 8–12 lines. It must not contain inline JSX layout, complex hooks, or business logic. It imports the module and exports the page:
   ```tsx
   // src/app/contact/page.tsx
   import { Contact } from "@/modules/Contact/Contact";

   export default function Page() {
     return <Contact />;
   }
   ```
4. **No 500-Line "God Files"**: Never let a page file grow beyond ~200 lines. Decompose into sub-views early using the two patterns below.

---

## 2. The Two Modular Page Patterns

### Pattern A: Simple Pages (Flat `Components/` Subfolder)
For standard utility pages (Contact, Privacy, Terms, Login) that have 1 to 5 pieces:

```text
src/modules/Contact/
├── Components/
│   ├── ContactForm.tsx       # Sub-component (Decomposed View)
│   ├── ContactInfo.tsx       # Sub-component (Decomposed View)
│   └── SocialLinks.tsx       # Sub-component (Decomposed View)
└── Contact.tsx               # Host Component (The Assembler)
```

- **Strict Barrel Rule**: **DO NOT create `index.ts` inside `Components/`**.
- The host file `Contact.tsx` imports directly from the named component:
  ```tsx
  import { ContactForm } from "./Components/ContactForm";
  import { ContactInfo } from "./Components/ContactInfo";
  ```

---

### Pattern B: Heavy Landing Pages (The `Folds/` Pattern)
For massive multi-section landing pages (like `Home`) containing 5+ sections:

```text
src/modules/Home/
├── Components/               # UI shared across folds (e.g. SectionBadge, StatPill)
├── Folds/                    # Major page sections
│   ├── index.ts              # THE ONLY fold barrel export (aggregates all folds)
│   ├── HeroSection/
│   │   ├── HeroGraphic.tsx   # Private sub-view helper
│   │   ├── HeroAction.tsx    # Private sub-view helper
│   │   └── HeroSection.tsx   # Fold Host Orchestrator
│   ├── About/
│   │   └── About.tsx         # Fold Host
│   ├── Projects/
│   │   ├── ProjectCard.tsx   # Sub-view
│   │   └── Projects.tsx      # Fold Host
│   └── Experience/
│       └── Experience.tsx    # Fold Host
└── Home.tsx                  # Main Host Assembler (imports folds from './Folds')
```

#### The Fold Host Assembler (`src/modules/Home/Home.tsx`):
```tsx
import { HeroSection, About, Projects, Experience } from "./Folds";

export function Home() {
  return (
    <main className="flex flex-col min-h-screen">
      <HeroSection />
      <About />
      <Projects />
      <Experience />
    </main>
  );
}
```

#### The Fold Barrel (`src/modules/Home/Folds/index.ts`):
```ts
export { HeroSection } from "./HeroSection/HeroSection";
export { About } from "./About/About";
export { Projects } from "./Projects/Projects";
export { Experience } from "./Experience/Experience";
```

---

## 3. Agent Execution Scenarios

When an agent is invoked with this skill, it must inspect the repository and determine which of the 3 scenarios applies:

```
                            ┌────────────────────────────┐
                            │ Scan src/app and existing  │
                            │ page/view directories      │
                            └─────────────┬──────────────┘
                                          │
             ┌────────────────────────────┼────────────────────────────┐
             ▼                            ▼                            ▼
   [FRESH / MINIMAL STARTER]      [SMALL REPO (1-3 PAGES)]     [LARGE / COMPLEX CODEBASE]
             │                            │                            │
   1. Scaffold Home (Folds)      1. Extract JSX into          1. DO NOT touch code blindly
   2. Scaffold Contact (Comps)      modules/Home & Contact    2. Draft migration plan
   3. Thin app/ page adapters    2. Make app/ pages thin      3. ASK user for confirmation
                                 3. Keep logic intact
```

### Scenario 1: Fresh / Minimal Starter (Default Vercel/Next.js Template)
- **Condition**: Only `src/app/page.tsx` exists with default template boilerplate.
- **Action**:
  1. Create `src/modules/Home/` with:
     - `src/modules/Home/Folds/HeroSection/HeroSection.tsx`
     - `src/modules/Home/Folds/About/About.tsx`
     - `src/modules/Home/Folds/index.ts`
     - `src/modules/Home/Home.tsx`
  2. Create a basic starter `src/modules/Contact/`:
     - `src/modules/Contact/Components/ContactForm.tsx`
     - `src/modules/Contact/Contact.tsx`
  3. Replace `src/app/page.tsx` with a thin adapter rendering `<Home />`.
  4. Create `src/app/contact/page.tsx` rendering `<Contact />`.

---

### Scenario 2: Small Existing Project (1–3 Simple Pages)
- **Condition**: 1 to 3 pages with inline JSX in `src/app/**/page.tsx`.
- **Action**:
  1. Migrate the code into `src/modules/[Feature]/[Feature].tsx`.
  2. For landing pages with multiple sections, decompose into `Folds/`.
  3. For simple pages, decompose into flat `Components/`.
  4. Turn `src/app/**/page.tsx` into thin 8-line adapters.
  5. Ensure zero functional regressions.

---

### Scenario 3: Large / Complex Established Codebase (Many Pages)
- **Condition**: Repository has 4+ routes, complex layouts, or deeply nested logic.
- **Action**:
  - **STRICTLY DO NOT MODIFY FILES BLINDLY.**
  - Analyze the existing structure and outline a step-by-step modular migration plan.
  - **Ask the user for explicit confirmation**:
    > *"Your codebase has [N] existing routes and [M] components. A full modular refactor requires restructuring these into `src/modules/`. Would you like me to proceed with migrating [Route X] first, or refactor all routes? [Confirm/Specify Scope]"*
  - Wait for user consent before executing file changes.
