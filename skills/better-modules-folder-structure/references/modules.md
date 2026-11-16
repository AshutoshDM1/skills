# Website Modules & Folds Architecture

This reference guide provides deep implementation details for structuring feature modules and folds inside `src/modules/`.

---

## 1. Directory Blueprint

```text
src/modules/
├── Home/                     # Multi-section Landing Page
│   ├── Components/           # Shared between folds (e.g. StatBadge, SectionDivider)
│   ├── Folds/                # Major page folds
│   │   ├── index.ts          # Aggregates and re-exports all fold components
│   │   ├── HeroSection/
│   │   │   ├── HeroGraphic.tsx
│   │   │   └── HeroSection.tsx
│   │   ├── About/
│   │   │   └── About.tsx
│   │   ├── TechStack/
│   │   │   └── TechStack.tsx
│   │   └── ContactSection/
│   │       └── ContactSection.tsx
│   └── Home.tsx              # Main Host Component (imports from './Folds')
│
└── Contact/                  # Simple Utility Page
    ├── Components/           # Flat sub-components (no index.ts!)
    │   ├── ContactForm.tsx
    │   └── ContactInfo.tsx
    └── Contact.tsx           # Host Component (The Assembler)
```

---

## 2. Naming Conventions

- **Module Folders**: Always **PascalCase** (`src/modules/Home`, `src/modules/Contact`, `src/modules/Blog`).
- **Host Component**: Matches folder name exactly (`Home/Home.tsx`, `Contact/Contact.tsx`).
- **Fold Folders**: Always **PascalCase** inside `Folds/` (`HeroSection/`, `About/`, `Projects/`).
- **Fold Host Component**: Matches fold directory name (`HeroSection/HeroSection.tsx`).

---

## 3. Thin App Router Integration

Next.js App Router files in `src/app/` must never contain layout styles or inline JSX:

```tsx
// src/app/page.tsx
import { Home } from "@/modules/Home/Home";

export default function Page() {
  return <Home />;
}
```

```tsx
// src/app/contact/page.tsx
import { Contact } from "@/modules/Contact/Contact";

export default function Page() {
  return <Contact />;
}
```

---

## 4. Import & Export Policies

1. **Host Component Export**: Always export the host component cleanly as a named export (or default export if preferred by project):
   ```tsx
   export function Home() { ... }
   ```
2. **Direct Imports for Simple Pages**: Callers import directly from the component file inside `Components/`:
   ```tsx
   import { ContactForm } from "./Components/ContactForm";
   ```
3. **Aggregated Imports for Folds**: `Home.tsx` imports all folds from `./Folds`:
   ```tsx
   import { HeroSection, About, TechStack, ContactSection } from "./Folds";
   ```
