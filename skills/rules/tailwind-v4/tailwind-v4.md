# Tailwind CSS v4 Modern Standards & Enforcement

## Core Principle

Write idiomatic **Tailwind CSS v4** only. Never emit v3 syntax, `tailwind.config.js` patterns, or arbitrary values (`[...]`) when v4 has a native utility, theme variable, or variant for the job.

**Decision order before writing any class:**
1. Is there a native v4 utility? Use it.
2. Is the value a design token? Define it in `@theme` and use the generated utility.
3. Is it a one-off CSS variable? Use the parenthesis syntax `bg-(--my-var)`.
4. Only then use an arbitrary value `[...]`.

---

## 1. Setup & Configuration (CSS-first)

- ✅ Entry CSS uses a single import, not the old directives:
  ```css
  @import "tailwindcss";
  ```
  ❌ `@tailwind base; @tailwind components; @tailwind utilities;`
- ✅ Configure in CSS: `@theme`, `@source`, `@plugin`, `@utility`, `@custom-variant`, `@variant`.
- ❌ Do not create or extend `tailwind.config.js`. Use `@config` only when migrating a legacy project.
- ✅ Build integration: `@tailwindcss/vite` (preferred) or `@tailwindcss/postcss`.
- ❌ No `autoprefixer`, `postcss-import`, or Sass/Less/Stylus preprocessors. v4 handles these.
- ✅ Content detection is automatic (respects `.gitignore`). Add paths with `@source "../node_modules/some-lib"`.
- ✅ Prefix, if needed: `@import "tailwindcss" prefix(tw);`, then `tw:flex tw:bg-red-500`.
- ✅ Migrating: run `npx @tailwindcss/upgrade`.
- In scoped styles (Vue/Svelte/CSS Modules) that use `@apply` or `@variant`, add `@reference "../app.css";`.

---

## 2. Design Tokens via `@theme`

Define tokens as CSS variables in the right namespace and v4 generates utilities automatically.

```css
@import "tailwindcss";

@theme {
  --font-display: "Satoshi", sans-serif;
  --color-brand-500: oklch(0.65 0.2 260);
  --breakpoint-3xl: 120rem;
  --radius-card: 1rem;
  --ease-snappy: cubic-bezier(0.2, 0, 0, 1);
  --animate-fade-in: fade-in 0.3s var(--ease-snappy);

  @keyframes fade-in {
    from { opacity: 0; }
    to   { opacity: 1; }
  }
}
```

Use: `font-display bg-brand-500 3xl:grid-cols-4 rounded-card animate-fade-in`.

- ✅ Use `@theme inline { ... }` when a token references another variable (e.g. shadcn-style `--color-background: var(--background)`).
- ✅ Reference tokens in CSS with `var(--color-brand-500)`.
- ❌ Do not use the `theme()` function. Use CSS variables instead.
- ✅ Prefer oklch for custom colors. The default palette is oklch and wide-gamut.

---

## 3. Renamed / Removed Utilities (Hard Rules)

| Legacy (v3), forbidden | Modern (v4), required |
| :--- | :--- |
| `bg-gradient-to-r` | `bg-linear-to-r` |
| `bg-[linear-gradient(45deg,...)]` | `bg-linear-45` |
| `z-[1]`, `z-[2]`, `z-[999]` | `z-1`, `z-2`, `z-999` |
| `shadow-sm` / `shadow` | `shadow-xs` / `shadow-sm` |
| `drop-shadow-sm` / `drop-shadow` | `drop-shadow-xs` / `drop-shadow-sm` |
| `blur-sm` / `blur` | `blur-xs` / `blur-sm` |
| `backdrop-blur-sm` / `backdrop-blur` | `backdrop-blur-xs` / `backdrop-blur-sm` |
| `rounded-sm` / `rounded` | `rounded-xs` / `rounded-sm` |
| `outline-none` | `outline-hidden` (v4 `outline-none` truly removes the outline) |
| `ring` (3px) | `ring-3` (v4 bare `ring` is 1px) |
| `bg-opacity-50`, `text-opacity-*`, `border-opacity-*` | `bg-black/50`, `text-white/70`, `border-red-500/20` |
| `flex-shrink-0` / `flex-grow` | `shrink-0` / `grow` |
| `overflow-ellipsis` | `text-ellipsis` |
| `decoration-slice` / `decoration-clone` | `box-decoration-slice` / `box-decoration-clone` |
| `h-4 w-4` | `size-4` |
| `bg-[--brand]` | `bg-(--brand)` |
| `!flex` (leading important) | `flex!` (trailing important) |
| `min-h-screen` | `min-h-dvh` (prefer dynamic viewport units) |
| `space-x-4` / `space-y-4` on flex/grid | `gap-4` |

---

## 4. Gradients

```html
<div class="bg-linear-to-r from-blue-500 to-purple-600"></div>
<div class="bg-linear-45 from-pink-500 to-rose-500"></div>
<div class="bg-radial from-white to-zinc-900"></div>
<div class="bg-conic from-red-500 via-yellow-400 to-red-500"></div>

<!-- Interpolation modifier for smoother, more vivid blends -->
<div class="bg-linear-to-r/oklch from-indigo-500 to-teal-400"></div>

<!-- Fully custom stops -->
<div class="bg-linear-[25deg,red_5%,yellow_60%,lime_90%,teal]"></div>
```

- ✅ Use `bg-radial` and `bg-conic` natively, not arbitrary `bg-[radial-gradient(...)]`.
- ✅ Use `via-none` to unset a `via-*` stop in a variant (e.g. `dark:via-none`).
- ✅ Prefer `/oklch` interpolation for vibrant gradients; use `/srgb` or `/hsl` only deliberately.

---

## 5. Spacing, Sizing & Numeric Values

v4 derives spacing from a single `--spacing` variable, so **any integer works**, with no arbitrary brackets.

- ✅ `w-17`, `mt-29`, `pr-7`, `grid-cols-15`, `z-1`, `z-999`, `opacity-35`
- ❌ `w-[68px]`, `grid-cols-[repeat(15,minmax(0,1fr))]`, `z-[1]`
- ✅ Fractions: `w-2/5`; `size-*` for equal width and height; `inset-*` / `inset-x-0` for positioning.
- ✅ Fluid typography with line-height shorthand: `text-sm/6`, `text-base/7`.
- ✅ `text-balance` for headings, `text-pretty` for paragraphs.

---

## 6. Colors & Borders (Changed Defaults)

- Default `border` and `ring` color is now `currentColor`. **Always specify a color explicitly**:
  `border border-gray-200`, `ring-3 ring-blue-500`.
- Placeholder color is now the current text color at 50%. Style explicitly with `placeholder:text-gray-400`.
- Buttons use `cursor: default`. Add `cursor-pointer` explicitly when you want it.
- Opacity always uses the slash modifier: `bg-blue-500/50`, never `bg-opacity-*`.
- Dark/light native UI: `scheme-light dark:scheme-dark`.

---

## 7. Variants

- ✅ Variants stack **left to right** in v4 (`first:*:pt-0`, not `*:first:pt-0`).
- ✅ Native new variants. Do not hand-roll with arbitrary selectors:
  - `not-*` → `not-hover:opacity-75`, `not-first:mt-4`
  - `has-*`, `group-has-*`, `peer-has-*` → `has-checked:bg-indigo-50`
  - `in-*` → `in-data-active:font-bold`
  - `nth-*` → `nth-3:bg-gray-50`, `nth-odd:bg-gray-50`
  - `inert:`, `open:`, `starting:` → `starting:opacity-0 transition-discrete`
  - `data-active:` (boolean) and `data-[state=open]:` (valued)
  - `aria-*`, `supports-*`, `pointer-fine:`, `pointer-coarse:`, `noscript:`
- ✅ Child targeting: `*:` (direct children) and `**:` (all descendants, v4.1+).
- `hover:` only applies on hover-capable devices by default. Don't add manual `@media (hover:hover)` wrappers.
- ✅ Custom class-based dark mode:
  ```css
  @custom-variant dark (&:where(.dark, .dark *));
  ```

---

## 8. Container Queries (Native, No Plugin)

```html
<div class="@container">
  <div class="grid grid-cols-1 @sm:grid-cols-2 @lg:grid-cols-4"></div>
</div>

<!-- Named containers -->
<div class="@container/sidebar">
  <div class="flex-col @md/sidebar:flex-row"></div>
</div>

<!-- Ranges and arbitrary thresholds -->
<div class="@min-[475px]:flex @max-md:hidden"></div>
```

- ❌ Do not install `@tailwindcss/container-queries`. It is built in.
- ✅ Prefer container queries over viewport breakpoints for reusable components.

---

## 9. 3D Transforms & Modern Effects

- ✅ `transform-3d`, `perspective-distant`, `rotate-x-12`, `rotate-y-45`, `translate-z-4`, `backface-hidden`
- ✅ Individual transform utilities: `scale-110`, `rotate-6`, `translate-x-2`, plus `scale-none` / `rotate-none` to reset. Do not use the legacy `transform` class.
- ✅ `inset-shadow-*`, `inset-ring-*`, `field-sizing-content`, `grid-cols-subgrid`
- ✅ v4.1+: `text-shadow-*`, `mask-*`, `wrap-break-word`, `items-center-safe` / `justify-center-safe`
- ✅ `transition-discrete` and `starting:` for enter/exit animations on `display` and popovers, instead of JS toggles.

---

## 10. Custom Utilities & Components

- ✅ Custom utilities use `@utility` so they work with every variant (`hover:`, `md:`, etc.):
  ```css
  @utility scrollbar-hidden {
    scrollbar-width: none;
    &::-webkit-scrollbar { display: none; }
  }

  @utility container {
    margin-inline: auto;
    padding-inline: 2rem;
  }
  ```
- ❌ Do not use `@layer utilities { .foo {} }` or `@layer components` for custom utilities.
- Keep `@apply` minimal. Prefer extracting framework components over CSS abstractions.

---

## 11. Tooling

- ✅ `prettier-plugin-tailwindcss` for class sorting (v4 compatible).
- ✅ `tailwind-merge` v3+ and `clsx` (the `cn()` helper) for conditional classes. Older tailwind-merge doesn't understand v4 class names.
- ✅ Dynamic classes must be complete strings. Never build them by interpolation (`` `bg-${color}-500` ``); map to full class names instead.
- Browser targets: Safari 16.4+, Chrome 111+, Firefox 128+. For older browsers, stay on v3.

---

## 12. Agent Checklist (Run Before Output)

Before returning any Tailwind code, verify:

- [ ] No `bg-gradient-to-*`; use `bg-linear-*`, `bg-radial`, `bg-conic`
- [ ] No `z-[n]`, `w-[Npx]`, or `grid-cols-[repeat(...)]` for plain integers
- [ ] No `bg-opacity-*` / `text-opacity-*`; use the `/50` modifier
- [ ] Shadow, blur, and rounded names are shifted (`shadow-xs`, `rounded-xs`, `blur-xs`)
- [ ] `outline-hidden`, not `outline-none`; `ring-3`, not bare `ring`, when 3px is intended
- [ ] Explicit border and ring colors
- [ ] `size-*` instead of paired `h-* w-*`
- [ ] `bg-(--var)` instead of `bg-[--var]`
- [ ] Trailing `!` for important
- [ ] `gap-*` instead of `space-x/y-*` on flex/grid
- [ ] Tokens live in `@theme`, not `tailwind.config.js`
- [ ] `@import "tailwindcss"`, not `@tailwind` directives
- [ ] Container queries use `@container` / `@sm:` with no plugin
- [ ] Custom utilities use `@utility`, not `@layer utilities`
- [ ] No string-interpolated class names