# Props-Based Architecture & Anti-Microfile Rule

This rule governs component structuring across the codebase. It strictly forbids unnecessary file fragmentation (micro-file sprawl) and enforces the **80–90% Props-Based archetype** as the production default.

---

## 1. The 80–90% Props-Based Archetype Law

In production frontends, **80–90% of UI components must be Props-Based Orchestrators** (black boxes driven by typed props).

```
Should the consumer assemble intermediate JSX slots directly in their template?
│
├── NO (80-90% of UI) ──► PROPS-BASED ARCHETYPE
│                         Pass data via typed props (<ProjectCard {...props} />).
│                         Keep implementation self-contained.
│
└── YES (10-20% of UI) ─► COMPOUND ARCHETYPE
                          Use strictly for flexible headless containers (<Tabs.List>, <Dialog.Content>).
```

### ❌ FORBIDDEN: Forcing Compound Patterns on Self-Contained Components
Do not create compound sub-components when a simple typed prop achieves the goal:

```tsx
// ❌ WRONG: Over-engineered compound pattern for a simple card
<StatCard>
  <StatCardHeader>
    <StatCardTitle>Revenue</StatCardTitle>
  </StatCardHeader>
  <StatCardContent>
    <StatCardValue>$50,000</StatCardValue>
  </StatCardContent>
</StatCard>
```

### ✅ REQUIRED: Props-Based Simplicity
```tsx
// ✅ CORRECT: Clean, declarative, and type-safe
<StatCard title="Revenue" value="$50,000" trend="+12%" />
```

---

## 2. The Single-File Threshold (< 300 Lines)

To prevent file fragmentation and "micro-file sprawl", follow strict line-count thresholds:

### Rule: Components < 300 lines MUST remain in a single file
- Keep the component, helper sub-views, and TypeScript interface in one file: `StatCard/StatCard.tsx`.
- ❌ **FORBIDDEN**: Creating a folder with 5 micro-files (`StatCardTitle.tsx`, `StatCardBadge.tsx`, `StatCardIcon.tsx`, `types.ts`, `index.ts`) for an 80-line component.

---

## 3. Decomposition Threshold (> 300–500 Lines)

When a component exceeds **300–500 lines** or contains multi-step internal state (e.g. a complex `SearchModal` with a query input, filter drawer, and result grid):

1. **Keep Sub-Views Private**: Extract internal sub-views into colocated sibling files inside the component directory:
   ```
   src/shared/SearchModal/
   ├── SearchModal.tsx        # Host orchestrator
   ├── SearchInput.tsx        # Private sub-view
   └── SearchResults.tsx      # Private sub-view
   ```
2. **No `index.ts` Leakage**:
   - Consumers only ever import the host file:
     ```tsx
     import { SearchModal } from "@/shared/SearchModal/SearchModal";
     ```
   - Do NOT add an `index.ts` that re-exports private internal sub-views.
