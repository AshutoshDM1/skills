---
name: better-modern-frontend-stack
description: Audits the project's frontend base stack against modern production standards (shadcn, Hugeicons, motion, Zustand, TanStack Query, Biome/Oxlint, animate-ui). Evaluates modernity, rates existing setups without changing code, prompts users on missing setups, or provides targeted upgrade roadmaps for half-baked stacks.
license: MIT
---

# Modern Frontend Base Stack Audit & Enforcer

This skill audits, diagnoses, and guides the adoption of a modern, production-grade, anti-slop frontend base stack.

---

## 1. The Modern Production Stack Spec

| Domain | Modern Standard (Must-Have) | Deprecated / Bad Practice | Why It Matters |
| :--- | :--- | :--- | :--- |
| **Component System** | `shadcn/ui` (pre-installed via `shadcn add --all`) | Custom raw `<div>` libraries or uninstalled UI | Having components in `@/components/ui` stops AI agents from hallucinating ugly unstyled divs. |
| **Icon System** | `hugeicons-react` | Overused `lucide-react` | Lucide is generic SaaS NPC energy. Hugeicons gives instant brand distinction. |
| **Animation** | `motion` (`motion/react`) | Legacy `framer-motion` | Lighter bundle, modern React 19 / RSC compatibility, zero baggage. |
| **Client State** | `zustand` | `redux`, `@reduxjs/toolkit`, Context abuse | Zero-boilerplate micro-stores; no boomer reducer/action boilerplate. |
| **Server State & Cache** | `@tanstack/react-query` + `axios` | Bare `useEffect` fetch loops | Eliminates double-fetching, race conditions, and un-cached network requests. |
| **Micro-Interactions** | `animate-ui` / `animate-ui-icons` | 50 lines of bespoke CSS `@keyframes` | Instant tactile polish for buttons, tabs, and toggles without design debt. |
| **Linting & Formatting** | `@biomejs/biome` or `oxlint` + Prettier | Sluggish 10-second ESLint configs | Sub-20ms instant feedback loops on save. |
| **Styling** | Tailwind CSS (v4 or CSS variables) | SCSS modules, inline CSS, Emotion | Native utility engine with instant compile speeds. |

---

## 2. Agent Execution Workflow

When running an audit or asked to check the frontend stack:

```
                          ┌───────────────────────────┐
                          │ Scan package.json,        │
                          │ components.json & src/    │
                          └─────────────┬─────────────┘
                                        │
           ┌────────────────────────────┼───────────────────────────┐
           ▼                            ▼                           ▼
  [ALL MODERN TOOLS PRESENT]   [NO MODERN STACK FOUND]     [HALF-BAKED / OUTDATED]
           │                            │                           │
  1. Calculate Score (9-10/10) 1. Explain benefits        1. Flag bad/outdated libs
  2. Rate & praise stack       2. ASK user if they want   2. Give modern replacement
  3. DO NOT TOUCH ANY CODE        the modern setup           commands
                               3. Wait for confirmation   3. Prioritize quick wins
```

---

## 3. The 3 Triage Scenarios

### Scenario 1: Fully Modern Setup Is Present
**Condition**: Project has `shadcn` with components populated, `hugeicons`, `motion`, `zustand`, `@tanstack/react-query`, and fast linting.
- **Action**: **STRICTLY READ-ONLY. DO NOT EDIT OR MODIFY ANY FILES.**
- **Output**:
  - Deliver a high rating (e.g., `9.5/10 - Production Anti-Slop Certified ⚡`).
  - Summarize what the project is doing right (clean component architecture, cached network layer, zero Redux bloat).
  - Highlight any minor optional optimizations without applying them.

---

### Scenario 2: Setup Is Not Present (Fresh or Raw Project)
**Condition**: No UI library initialized, no client store, raw `fetch` calls, default Vite/Next.js skeleton.
- **Action**: **DO NOT BLINDLY GENERATE CODE.**
- **Output**:
  - Inform the user that the project is running on bare essentials.
  - Present the value proposition in 3 bullet points (AI grounding, no re-rendering bugs, brand polish).
  - **Ask the user explicitly**: *"Would you like me to scaffold the modern frontend base stack (Shadcn + Hugeicons + Motion + Zustand + TanStack Query)? [Yes/No]"*
  - Wait for user confirmation before executing any install commands.

---

### Scenario 3: Setup Is Half-Baked or Outdated
**Condition**: Project has mixed signals (e.g. `framer-motion` instead of `motion`, `lucide-react` only, Redux installed alongside Zustand, or `components.json` exists but `@/components/ui/` only has 2 components).
- **Action**: Diagnose, explain the flaws, and provide exact drop-in replacements.
- **Output Structure**:
  1. **The Diagnostic Roast (Professional & Direct)**:
     - ❌ **Legacy Animation**: Found `framer-motion`. Migrate to `motion` (`motion/react`) for smaller bundle and cleaner RSC support.
     - ❌ **Missing Component Arsenal**: Found `components.json`, but only `button.tsx` is installed. AI agents will hallucinate custom divs for modals and dropdowns.
     - ❌ **Uncached Server State**: Found raw `fetch()` inside `useEffect`. Add `@tanstack/react-query` to prevent waterfalls.
     - ❌ **Icon Fatigue**: Found `lucide-react`. Suggest upgrading to `hugeicons-react` for distinct visual craft.
  2. **The Modern Upgrade Command**:
     Provide the exact copy-paste installation commands:
     ```bash
     pnpm add motion zustand @tanstack/react-query axios hugeicons-react animate-ui
     pnpm dlx shadcn@latest add --all
     ```
  3. Ask the user if they want help refactoring specific files to the modern standard.

---

## 4. Scoring Rubric

| Criteria | Points | Check Logic |
| :--- | :--- | :--- |
| **Shadcn Installed & Fully Populated** | 25 pts | `components.json` exists AND >15 files in `components/ui/` |
| **Modern Icons (`hugeicons-react`)** | 15 pts | `hugeicons-react` in `dependencies` |
| **Modern Motion (`motion`)** | 15 pts | `motion` in `dependencies` (Deduct 5 if using `framer-motion`) |
| **Lean State (`zustand`)** | 15 pts | `zustand` present (Deduct 10 if `redux` or `@reduxjs/toolkit` present) |
| **Server State Caching (`@tanstack/react-query`)** | 15 pts | `@tanstack/react-query` + QueryProvider configured |
| **High-Speed Linting (`biome` or `oxlint`)** | 10 pts | `@biomejs/biome` or `oxlint` in `devDependencies` |
| **Micro-Interactions (`animate-ui`)** | 5 pts | `animate-ui` present |

- **90–100 pts**: 🏆 **Elite Tier**: Anti-slop production standard. Do not modify.
- **50–89 pts**: ⚠️ **Half-Cooked**: Needs modernization (list replacements).
- **< 50 pts**: 🛑 **Legacy / Blank**: Prompt user for complete modern stack scaffold.
