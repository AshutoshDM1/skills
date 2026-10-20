# Lean Modules Architecture Guide

This guide defines the standards for building **Lean Modules** in Next.js applications (ideal for landing pages, marketing sites, portfolios, legal pages, and content-driven web apps).

---

## 1. Core Philosophy: The Lean Blueprint

Lean modules prioritize fast rendering, pristine Next.js App Router decoupling, and zero boilerplate:

- **No Empty Folders**: Lean modules do not have empty `hooks/`, `services/`, or `types/` directories.
- **No Root Index Sprawl**: Callers and routes import directly from `@/modules/[Feature]/[Feature]`.
- **Flat Components vs. Folds**:
  - Small pages (0–5 components) use a **flat `components/`** directory.
  - Multi-section landing pages use **`Folds/`** to encapsulate major page sections and sub-elements.

---

## 2. Directory Layouts

### Pattern A: Simple Lean Module (0–5 Components, e.g. TermsAndConditions, PrivacyPolicy, CookiePolicy, Contact)

```text
src/modules/TermsAndConditions/
├── components/
│   └── TermsAndConditionsContent.tsx # Flat component file (no subfolder / no index.ts)
└── TermsAndConditions.tsx            # Root page assembler
```

```text
src/modules/Contact/
├── components/
│   ├── ContactForm.tsx       # Flat component
│   ├── ContactInfo.tsx       # Flat component
│   └── ContactInput.tsx      # Flat component
└── Contact.tsx               # Root page assembler
```

---

### Pattern B: Multi-Fold Landing Page (e.g. Home)

When a page consists of multiple major page folds/sections (Hero, About, TechStack, WhyChooseUs, HowWeWork, FAQ, CTA), name the folder **`Folds/`** (PascalCase):

```text
src/modules/Home/
├── Folds/
│   ├── HeroSection/
│   │   ├── HeroSection.tsx
│   │   └── HeroBackground.tsx
│   ├── AboutUs/
│   │   └── AboutUs.tsx
│   ├── TechStack/
│   │   └── TechStack.tsx
│   ├── WhyChooseUs/
│   │   └── WhyChooseUs.tsx
│   ├── HowWeWork/
│   │   └── HowWeWork.tsx
│   ├── Faq/
│   │   └── Faq.tsx
│   ├── Cta/
│   │   └── Cta.tsx
│   └── index.ts              # Aggregates all major folds
└── Home.tsx                  # Root page assembler importing from './Folds'
```

---

## 3. Code Implementations

### Root Assembler (`src/modules/Home/Home.tsx`):

```tsx
import { Navbar } from '@/shared/Navbar/Navbar';
import { Footer } from '@/shared/Footer/Footer';
import { HeroSection, AboutUs, TechStack, HowWeWork, WhyChooseUs, Faq, Cta } from './Folds';

export default function Home() {
  return (
    <>
      <Navbar />
      <HeroSection />
      <AboutUs />
      <TechStack />
      <HowWeWork />
      <WhyChooseUs />
      <Faq />
      <Cta />
      <Footer />
    </>
  );
}
```

### Thin App Router Adapter (`src/app/page.tsx`):

```tsx
import type { Metadata } from 'next';
import Home from '@/modules/Home/Home';
import { homeMetadata } from '@/metadata';

export const metadata: Metadata = homeMetadata;

export default function Page() {
  return <Home />;
}
```

---

## 4. Architectural Rules

1. **PascalCase Directories**: `src/modules/Home/`, `src/modules/Contact/`, `src/modules/Terms/`.
2. **Direct Imports**: Always import `@/modules/[Feature]/[Feature]`. Never create root `index.ts` files inside module directories.
3. **Encapsulation**: Never import a fold or component from another module. If shared, promote it to `src/shared/[ComponentName]/`.
