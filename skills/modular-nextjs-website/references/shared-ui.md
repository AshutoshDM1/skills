# Shared UI & Component Hierarchy Guide

This guide defines the component layering hierarchy for Lean Next.js projects.

---

## 1. The 3-Tier Hierarchy

```text
┌─────────────────────────────────────────────────────────────┐
│ Tier 1: Module Components / Folds                           │
│ (src/modules/[Feature]/components or Folds)                 │
└──────────────────────────────┬──────────────────────────────┘
                               │ uses
┌──────────────────────────────▼──────────────────────────────┐
│ Tier 2: Shared Brand/Layout Blocks (src/shared/)            │
│ Reusable composite blocks (Navbar, Footer, SectionBadge)    │
└──────────────────────────────┬──────────────────────────────┘
                               │ uses
┌──────────────────────────────▼──────────────────────────────┐
│ Tier 3: UI Primitives (src/components/ui/)                  │
│ Headless / Atomic design system components (Button, Dialog) │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. Directory Layouts

### `src/shared/` (Composite Reusable Blocks)

```text
src/shared/
├── Navbar/
│   └── Navbar.tsx
├── Footer/
│   └── Footer.tsx
├── SectionBadge/
│   └── SectionBadge.tsx
├── Section/
│   └── Section.tsx
└── index.ts              # Shared barrel export
```

### `src/components/ui/` (Atomic Primitives)

```text
src/components/
├── ui/
│   ├── button.tsx
│   ├── dialog.tsx
│   └── input.tsx
└── theme-provider.tsx
```

---

## 3. Decision Matrix

| Question                                                                          | Destination                                          |
| :-------------------------------------------------------------------------------- | :--------------------------------------------------- |
| Is it an atomic headless primitive (Button, Input, Tooltip)?                      | `src/components/ui/`                                 |
| Is it a brand/layout block used on multiple pages (Navbar, Footer, SectionBadge)? | `src/shared/`                                        |
| Is it a small component unique to 1 page (0–5 components)?                        | `src/modules/[Feature]/components/[Name].tsx` (Flat) |
| Is it a major section of a landing page?                                          | `src/modules/[Feature]/Folds/[SectionName]/`         |
