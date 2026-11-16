---
name: better-shared-folder-structure
description: Architecture standards, starter block generators, and module integration workflows for src/shared/. Enforces composite brand components, the universal 8 starter blocks (Section, BrandButton, HeaderText, DescriptionText, Loader, ErrorPage, Breadcrumb, Icons), barrel re-exports, and safe reuse inside feature modules.
license: MIT
---

# Shared Folder Structure Architecture (`src/shared/`)

This skill governs the creation, architecture, and integration of the **Shared Brand Component System** inside `src/shared/`.

---

## 1. Core Architectural Laws

1. **Brand Identity vs. Atomic Primitives**:
   - `src/components/ui/` = Raw headless/unstyled primitives (Shadcn Button, Dialog, Popover).
   - `src/shared/` = Custom composite components carrying brand identity, styling, and consistent layout rhythm across multiple pages.
2. **PascalCase Directory Names**: Every component folder inside `src/shared/` is strictly **PascalCase** (`src/shared/BrandButton/`, `src/shared/Section/`).
3. **The Public Barrel (`src/shared/index.ts`)**: Because consumers frequently import multiple shared brand blocks simultaneously (`Section`, `HeaderText`, `BrandButton`), `src/shared/index.ts` is **REQUIRED** and re-exports all public shared components.
4. **Colocated Contracts**: If a shared component has custom TypeScript interfaces, colocate them inside the folder (`Section/SectionTypes.ts` or `Section/types.ts`).

---

## 2. Directory Anatomy

```text
src/shared/
├── index.ts                  # Public barrel export
├── Section/
│   ├── SectionTypes.ts       # Colocated interface
│   └── Section.tsx           # Standardized container wrapper (max-w-7xl)
├── BrandButton/
│   └── BrandButton.tsx       # Animated CTA button with brand glow/hover
├── HeaderText/
│   └── HeaderText.tsx        # Standardized typographic title
├── DescriptionText/
│   └── DescriptionText.tsx   # Standardized muted sub-headline
├── Loader/
│   └── Loader.tsx            # Brand loading spinner / skeleton wrapper
├── ErrorPage/
│   └── ErrorPage.tsx         # 404 / 500 error boundary screen
├── Breadcrumb/
│   └── Breadcrumb.tsx        # Route navigation breadcrumbs
└── Icons/                    # Custom SVG logos / socials
    ├── Github.tsx
    ├── Twitter.tsx
    └── index.ts
```

---

## 3. The 8 Universal Starter Blocks (Production Code)

### 1. `Section` (`src/shared/Section/Section.tsx`)
Enforces uniform vertical rhythm (`py-16 md:py-24`) and modern responsive maximum width (`max-w-7xl mx-auto px-4 sm:px-6 lg:px-8`):

```tsx
import { cn } from "@/lib/utils";
import React from "react";

export interface SectionProps extends React.HTMLAttributes<HTMLElement> {
  id?: string;
  className?: string;
  children: React.ReactNode;
}

export function Section({ id, className, children, ...props }: SectionProps) {
  return (
    <section
      id={id}
      className={cn("w-full max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-16 md:py-24", className)}
      {...props}
    >
      {children}
    </section>
  );
}
```

---

### 2. `BrandButton` (`src/shared/BrandButton/BrandButton.tsx`)
Polished interactive CTA with micro-spring active feedback:

```tsx
import { cn } from "@/lib/utils";
import React from "react";

export interface BrandButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  children: React.ReactNode;
  className?: string;
  variant?: "primary" | "secondary" | "outline";
}

export function BrandButton({
  children,
  className,
  variant = "primary",
  ...props
}: BrandButtonProps) {
  const baseStyles =
    "inline-flex items-center justify-center gap-2 rounded-xl px-5 py-2.5 text-sm font-medium transition-all duration-200 active:scale-95 disabled:opacity-50 disabled:pointer-events-none";

  const variants = {
    primary: "bg-primary text-primary-foreground shadow-sm hover:opacity-90",
    secondary: "bg-secondary text-secondary-foreground hover:bg-secondary/80",
    outline: "border border-border bg-background hover:bg-muted text-foreground",
  };

  return (
    <button className={cn(baseStyles, variants[variant], className)} {...props}>
      {children}
    </button>
  );
}
```

---

### 3. `HeaderText` (`src/shared/HeaderText/HeaderText.tsx`)
Responsive title with crisp typography hierarchy:

```tsx
import { cn } from "@/lib/utils";
import React from "react";

export interface HeaderTextProps extends React.HTMLAttributes<HTMLHeadingElement> {
  children: React.ReactNode;
  className?: string;
  as?: "h1" | "h2" | "h3";
}

export function HeaderText({
  children,
  className,
  as: Component = "h2",
  ...props
}: HeaderTextProps) {
  return (
    <Component
      className={cn(
        "text-3xl sm:text-4xl md:text-5xl font-bold tracking-tight text-foreground",
        className
      )}
      {...props}
    >
      {children}
    </Component>
  );
}
```

---

### 4. `DescriptionText` (`src/shared/DescriptionText/DescriptionText.tsx`)
Muted sub-headline guaranteeing high readability:

```tsx
import { cn } from "@/lib/utils";
import React from "react";

export interface DescriptionTextProps extends React.HTMLAttributes<HTMLParagraphElement> {
  children: React.ReactNode;
  className?: string;
}

export function DescriptionText({
  children,
  className,
  ...props
}: DescriptionTextProps) {
  return (
    <p
      className={cn(
        "text-base sm:text-lg text-muted-foreground max-w-2xl leading-relaxed",
        className
      )}
      {...props}
    >
      {children}
    </p>
  );
}
```

---

### 5. `Loader` (`src/shared/Loader/Loader.tsx`)
Clean loading spinner for Suspense boundaries and async transitions:

```tsx
import { cn } from "@/lib/utils";

export function Loader({ className }: { className?: string }) {
  return (
    <div className={cn("flex items-center justify-center p-8", className)}>
      <div className="h-8 w-8 animate-spin rounded-full border-2 border-primary border-t-transparent" />
    </div>
  );
}
```

---

### 6. `ErrorPage` (`src/shared/ErrorPage/ErrorPage.tsx`)
Universal error fallback screen:

```tsx
import Link from "next/link";
import { Section } from "../Section/Section";
import { HeaderText } from "../HeaderText/HeaderText";
import { DescriptionText } from "../DescriptionText/DescriptionText";
import { BrandButton } from "../BrandButton/BrandButton";

export function ErrorPage({
  title = "Page Not Found",
  description = "The page you are looking for doesn't exist or has been moved.",
}: {
  title?: string;
  description?: string;
}) {
  return (
    <Section className="flex flex-col items-center justify-center text-center min-h-[60vh] gap-4">
      <HeaderText as="h1">{title}</HeaderText>
      <DescriptionText>{description}</DescriptionText>
      <Link href="/">
        <BrandButton className="mt-4">Return Home</BrandButton>
      </Link>
    </Section>
  );
}
```

---

### 7. `Breadcrumb` (`src/shared/Breadcrumb/Breadcrumb.tsx`)
Sub-page navigation helper:

```tsx
import Link from "next/link";
import React from "react";
import { cn } from "@/lib/utils";

export interface BreadcrumbItem {
  label: string;
  href?: string;
}

export function Breadcrumb({
  items,
  className,
}: {
  items: BreadcrumbItem[];
  className?: string;
}) {
  return (
    <nav className={cn("flex items-center gap-2 text-sm text-muted-foreground", className)}>
      <Link href="/" className="hover:text-foreground transition-colors">
        Home
      </Link>
      {items.map((item, index) => (
        <React.Fragment key={index}>
          <span>/</span>
          {item.href ? (
            <Link href={item.href} className="hover:text-foreground transition-colors">
              {item.label}
            </Link>
          ) : (
            <span className="text-foreground font-medium">{item.label}</span>
          )}
        </React.Fragment>
      ))}
    </nav>
  );
}
```

---

### 8. `Icons` (`src/shared/Icons/index.ts`)
Custom brand SVGs (GitHub, Twitter, Discord) not provided by icon libraries:

```tsx
// src/shared/Icons/Github.tsx
export function GithubIcon({ className = "h-5 w-5" }: { className?: string }) {
  return (
    <svg
      className={className}
      viewBox="0 0 24 24"
      fill="none"
      stroke="currentColor"
      strokeWidth="2"
      strokeLinecap="round"
      strokeLinejoin="round"
    >
      <path d="M15 22v-4a4.8 4.8 0 0 0-1-3.5c3 0 6-2 6-5.5.08-1.25-.27-2.48-1-3.5.28-1.15.28-2.35 0-3.5 0 0-1 0-3 1.5-2.64-.5-5.36-.5-8 0C6 2 5 2 5 2c-.3 1.15-.3 2.35 0 3.5A5.403 5.403 0 0 0 4 9c0 3.5 3 5.5 6 5.5-.39.49-.68 1.05-.85 1.65-.17.6-.22 1.23-.15 1.85v4" />
      <path d="M9 18c-4.51 2-5-2-7-2" />
    </svg>
  );
}
```

---

### 9. The Master Barrel (`src/shared/index.ts`)
```ts
export { Section } from "./Section/Section";
export { BrandButton } from "./BrandButton/BrandButton";
export { HeaderText } from "./HeaderText/HeaderText";
export { DescriptionText } from "./DescriptionText/DescriptionText";
export { Loader } from "./Loader/Loader";
export { ErrorPage } from "./ErrorPage/ErrorPage";
export { Breadcrumb } from "./Breadcrumb/Breadcrumb";
export * from "./Icons";
```

---

## 4. Integration Workflow with `src/modules/`

Whenever this skill is executed:

1. **Check & Scaffold `src/shared/`**: Ensure the 8 core blocks and `src/shared/index.ts` exist.
2. **Scan `src/modules/`**:
   - Replace repetitive ad-hoc section containers with `<Section>`.
   - Replace raw heading tags (`<h2 className="...">`) with `<HeaderText>`.
   - Replace raw description paragraphs with `<DescriptionText>`.
   - Replace generic buttons with `<BrandButton>`.
3. **Consumption Syntax**:
   ```tsx
   import { Section, HeaderText, DescriptionText, BrandButton } from "@/shared";

   export function HeroSection() {
     return (
       <Section className="text-center">
         <HeaderText as="h1">Build Without Slop</HeaderText>
         <DescriptionText className="mx-auto mt-4">
           Production architecture designed for real-world scalability.
         </DescriptionText>
         <BrandButton className="mt-8">Get Started</BrandButton>
       </Section>
     );
   }
   ```

---

## 5. Execution Safeguards

- **Fresh / Small Project (0–3 pages)**: Automatically create `src/shared/` and integrate into existing pages/modules.
- **Large / Complex Codebase (Many pages)**: **DO NOT touch code blindly**. Scan the repo, generate an integration plan, and **ask the user for explicit confirmation** before replacing components.
