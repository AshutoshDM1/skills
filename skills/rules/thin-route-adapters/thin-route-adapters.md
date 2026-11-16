# Thin Route Adapters & Feature Isolation Rule

This rule strictly dictates how Next.js App Router (`src/app/`) interacts with feature modules (`src/modules/`), enforcing clean separation between routing and UI state.

---

## 1. The Thin Adapter Law (`page.tsx`)

`src/app/**/page.tsx` must strictly serve as a **thin routing adapter** (typically under 10–15 lines of code).

### ❌ FORBIDDEN (Bloated Page Anti-Pattern)
Never write raw layout JSX, interactive state hooks (`useState`, `useEffect`), or inline sections directly in `page.tsx`:

```tsx
// ❌ WRONG: Bloated 100+ line page.tsx
export default function Page() {
  const [data, setData] = useState(null);
  return (
    <main className="min-h-screen px-4 py-8">
      <section className="hero">
        <h1>Welcome</h1>
        {/* 150 lines of JSX */}
      </section>
      <section className="about">
        {/* another 100 lines of JSX */}
      </section>
    </main>
  );
}
```

### ✅ REQUIRED (Thin Adapter Pattern)
All UI logic, state, and sections must live inside `src/modules/[Feature]/[Feature].tsx`. The route adapter only imports and renders the feature module:

```tsx
// ✅ CORRECT: Clean 8-line route adapter
import { Home } from "@/modules/Home/Home";
import { homeMetadata } from "@/metadata";

export const metadata = homeMetadata;

export default function Page() {
  return <Home />;
}
```

---

## 2. PascalCase Feature Modules

Every directory inside `src/modules/` represents a domain feature and **MUST be PascalCase**:

- ✅ `src/modules/Home/`
- ✅ `src/modules/Contact/`
- ✅ `src/modules/Pricing/`
- ✅ `src/modules/UserDashboard/`
- ❌ `src/modules/home/`
- ❌ `src/modules/contact-us/`
- ❌ `src/modules/pricingPage/`

---

## 3. Strict Feature Encapsulation (Zero Cross-Module Leakage)

Features must be self-contained modules. Private implementation details of one feature must never be imported into another:

- ❌ **FORBIDDEN**:
  ```tsx
  // Inside src/modules/About/About.tsx
  import { HeroSection } from "@/modules/Home/Folds/HeroSection"; // LEAK!
  ```
- ✅ **REQUIRED**:
  - If a component or helper is needed across 2 or more features, lift it into **`src/shared/`**.
  - A feature module only exposes its top-level entry component (`@/modules/Home/Home`).
