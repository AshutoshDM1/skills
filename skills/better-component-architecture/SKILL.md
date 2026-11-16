---
name: better-component-architecture
description: Guides the creation and refactoring of UI components following production architecture standards (Compound vs. Props-Based archetypes, Single-File vs. Multi-File thresholds, barrel file rules). Interactively prompts the user to determine the component's scope, archetype, and structure before modifying or generating code.
license: MIT
---

# Component Architecture & Creation Guide

This skill governs how components are architected, structured, created, and refactored across the codebase. It enforces production-grade boundaries between **Props-Based Orchestrators** (80–90% of UI) and **Compound Components** (10% of UI), while maintaining strict file-size thresholds and barrel import rules.

---

## 1. The Two Component Archetypes

### Archetype A: Props-Based Component (The 80–90% Production Workhorse)
**Examples**: `BrandButton`, `SearchModal`, `ProfileCard`, `ProjectCard`  
The component is an **autonomous black-box**. The consumer drops in a single tag and configures everything via props:
```tsx
<BrandButton variant="primary" onClick={handleSave}>
  Save Changes
</BrandButton>

<ProjectCard
  title="Elitedev Platform"
  description="Next-gen developer portfolio"
  badge="Featured"
/>
```

#### Sizing & File Rules:
1. **Single-File Pattern (< 300 Lines — Strictly Preferred)**:
   Keep the entire component in a single file inside its PascalCase folder:
   ```text
   src/shared/BrandButton/
   └── BrandButton.tsx
   ```
2. **Multi-File Pattern (> 300–500 Lines — When Complex)**:
   When internal logic, sub-views, or animations cause the component to balloon beyond 300 lines, decompose it into private colocated files:
   ```text
   src/shared/SearchModal/
   ├── SearchTrigger.tsx      # Private sub-view (not exported outside)
   ├── SearchList.tsx         # Private sub-view (not exported outside)
   ├── types.ts               # Colocated contracts
   └── SearchModal.tsx        # COMPULSORY Host Component (THE ONLY PUBLIC ENTRY POINT)
   ```
3. **The Strict No-Barrel Law**:
   - 🛑 **DO NOT CREATE `index.ts` for Props-Based Components!**
   - Direct file imports prevent leaking private sub-views to other parts of the application:
     ```tsx
     import { SearchModal } from "@/shared/SearchModal/SearchModal";
     ```

---

### Archetype B: Compound Component (The 10% Exception)
**Examples**: `Tabs`, `Dialog`, `Accordion`, `Select`, multi-slot `Card`  
The consumer assembles multiple collaborative components together in JSX (*Inversion of Control*):
```tsx
<Card>
  <CardHeader title="Analytics" />
  <CardBody>Real-time traffic overview</CardBody>
  <CardFooter>Last updated 2m ago</CardFooter>
</Card>
```

#### Sizing & File Rules:
1. **Single-File Pattern (< 300 Lines — Preferred)**:
   A single file can export the container along with its sub-components:
   ```text
   src/shared/Card/
   └── Card.tsx     # Exports Card, CardHeader, CardBody, CardFooter
   ```
2. **Multi-File Pattern (> 300 Lines — Decomposed)**:
   When sub-components are individually heavy, split them into dedicated files:
   ```text
   src/shared/Card/
   ├── index.ts          # REQUIRED: Public barrel exporting all pieces
   ├── CardHeader.tsx
   ├── CardBody.tsx
   ├── CardFooter.tsx
   ├── CardContext.tsx   # Optional scoped React context
   └── Card.tsx          # Container or all-in-one convenience host
   ```
3. **The Compulsory Barrel Law**:
   - 💡 **`index.ts` IS REQUIRED for Compound Components**.
   - Because callers must import 3 to 4 pieces at once, the barrel provides a clean single-line import:
     ```tsx
     import { Card, CardHeader, CardBody, CardFooter } from "@/shared/Card";
     ```

---

## 2. Decision Matrix: Single File vs. Multi-File

```text
                  Is it a Compound or Props-Based component?
                                     │
            ┌────────────────────────┴────────────────────────┐
            ▼                                                 ▼
    [COMPOUND COMPONENT]                             [PROPS-BASED COMPONENT]
    Caller imports multiple JSX parts                 Caller drops in single tag
            │                                                 │
   Lines < 300?                                      Lines < 300-500?
   ├── YES ──► Single File (Card/Card.tsx)            ├── YES ──► Single File (BrandButton.tsx)
   └── NO  ──► Multi-File + index.ts                  └── NO  ──► Multi-File (NO index.ts)
               (CardHeader, Body, Footer)                         (Host + Private Sub-views)
```

---

## 3. Interactive Agent Execution Workflows (Always Prompt the User)

Whenever an agent is invoked with this skill, it must **never write or move files silently**. It must identify whether the task is **Creation** or **Refactoring** and prompt the user:

### Mode 1: Creating a New Component
Before generating any files, the agent must ask the user:
1. **Target Location**:
   - `src/shared/[Name]/` (Brand component used across multiple pages)
   - `src/modules/[Feature]/Components/[Name].tsx` (Scoped to a single feature page)
   - `src/components/ui/` (Headless atomic primitive)
2. **Component Archetype**:
   - **Props-Based** (Recommended default: black-box taking props)
   - **Compound** (Multi-part JSX assembly with `<Parent><Child/></Parent>`)
3. **Initial Sizing**:
   - **Single File** (Default: under 300 lines)
   - **Multi-File Decomposed** (If known upfront to be complex with 3+ distinct sub-views)

---

### Mode 2: Refactoring an Existing Component
When invoked on an existing component (e.g. bloated file, messy barrel imports, or architectural drift):
1. **Analyze First**:
   - Check line count (is it > 300–500 lines?).
   - Check import patterns (does it have an unnecessary `index.ts` leaking private files?).
   - Check archetype (is it a props-based component masquerading as a compound component?).
2. **Ask the User Before Executing**:
   Present the diagnostic findings and ask:
   > *"Component `[Name]` has [X] lines. I propose refactoring it as a **[Props-Based / Compound]** component by:*\n\n"
   > *1. Breaking out `[SubView1]` and `[SubView2]` as private helpers.*\n"
   > *2. [Removing / Adding] `index.ts` to follow our barrel policy.*\n"
   > *3. Updating [N] callers across the codebase.*\n\n"
   > *"Would you like me to proceed with this plan? [Yes/Customize]"*
3. **Execute After Approval**:
   Decompose the files, apply clean TypeScript interfaces, verify that caller imports remain intact, and test for zero regressions.

---

## 4. Code Review & Enforcement Checklist

<todo checked="false">Default to Props-Based: 80-90% of components should take props, not require manual JSX assembly.</todo>
<todo checked="false">Single-File First: Keep under 300 lines in a single file before creating multi-file folders.</todo>
<todo checked="false">No Barrel for Props-Based: Never create index.ts for black-box orchestrators; import from the named file directly.</todo>
<todo checked="false">Barrel for Compound: Include index.ts when callers need 2+ peer JSX tags.</todo>
<todo checked="false">Private Colocation: Sub-views, context, and types stay private inside the component directory.</todo>
<todo checked="false">Prompt User: Always confirm archetype and structure before creating or refactoring components.</todo>
