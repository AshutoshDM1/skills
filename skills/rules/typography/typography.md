# Typography & Text Sizing Standards

## Core Principle

Avoid overly cramped, microscopic text. A common anti-pattern is defaulting to `text-xs` everywhere, which makes interfaces look amateurish, illegible, and visually weak.

Always favor confident, readable typography using **`base` + `sm`** for core content, and responsive pairs like **`text-sm md:text-base`** or **`text-xs md:text-sm`**.

---

## Typography Hierarchy Rules

### 1. Component Titles & Section Headings
Never use `text-base` for primary titles or prominent section headers — they lack hierarchy and look too small.
- ❌ **AVOID**:
  ```tsx
  <h1 className="text-base font-semibold">Blur Shimmer Text</h1>
  <h2 className="text-base font-semibold">Installation</h2>
  <h2 className="text-base font-semibold">Usage</h2>
  ```
- ✅ **USE**:
  ```tsx
  <h1 className="text-lg md:text-xl font-semibold text-foreground">
    Blur Shimmer Text
  </h1>
  <h2 className="text-lg font-semibold text-foreground">
    Installation
  </h2>
  <h2 className="text-base md:text-lg font-semibold text-foreground">
    Usage
  </h2>
  ```

---

### 2. Descriptions & Body Text
Descriptions should be comfortable to read, not compressed into tiny captions.
- ❌ **AVOID**:
  ```tsx
  <p className="text-xs sm:text-sm text-muted-foreground">
  ```
- ✅ **USE**:
  ```tsx
  <p className="text-sm sm:text-base text-muted-foreground leading-relaxed">
  ```

---

### 3. Tabs, Navigation, & Action Buttons
Interactive elements need enough visual presence and touch target clarity.
- ❌ **AVOID**:
  ```tsx
  <button className="text-xs font-medium">Command</button>
  <button className="text-xs font-medium">Manual</button>
  ```
- ✅ **USE**:
  ```tsx
  <button className="text-sm md:text-base font-medium">Command</button>
  <button className="text-sm md:text-base font-medium">Manual</button>
  ```

---

### 4. Step-by-Step Steppers & Instructions
Labels explaining steps or dependencies should be readable at standard viewing distance.
- ❌ **AVOID**:
  ```tsx
  <div className="size-7 text-xs">1</div>
  <p className="text-xs">Install the following dependencies:</p>
  ```
- ✅ **USE**:
  ```tsx
  <div className="size-7 rounded-lg text-sm font-medium">1</div>
  <p className="text-sm md:text-base font-medium text-foreground">
    Install the following dependencies:
  </p>
  ```

---

### 5. Tables & Secondary Metadata (API Reference, Properties)
Never lock entire tables to static `text-xs`. Use responsive stepping so desktop screens enjoy legible `text-sm`.
- ❌ **AVOID**:
  ```tsx
  <table className="text-xs">
    <td className="text-xs">{item.description}</td>
  </table>
  ```
- ✅ **USE**:
  ```tsx
  <table className="text-xs md:text-sm">
    <td className="text-xs md:text-sm text-muted-foreground/90">
      {item.description}
    </td>
  </table>
  ```

---

### 6. When to Use `text-xs`
Reserve `text-xs` strictly for true microcopy:
- Keyboard shortcuts (`kbd`)
- Small status tags / badges (e.g. `ONGOING`, `PRO`)
- Timestamps in tight lists
- Compact dropdown menu items with high density

---

## Quick Reference Scale

| Element Type | Avoid (Too Cramped) | Required Standard |
| :--- | :--- | :--- |
| **Page / Component Title** | `text-base` | `text-lg` or `text-xl` |
| **Section Headings (H2)** | `text-sm` / `text-base` | `text-lg` or `text-base md:text-lg` |
| **Primary Description** | `text-xs` / `text-sm` | `text-sm sm:text-base` |
| **Tabs & Stepper Labels** | `text-xs` | `text-sm md:text-base` |
| **Stepper Badges** | `text-xs` | `text-sm font-medium` |
| **Table Cells & Notes** | `text-xs` (rigid) | `text-xs md:text-sm` |
| **Micro Badges / Kbd** | `text-[8px]` | `text-[10px]` or `text-xs` |

