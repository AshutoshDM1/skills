# Tailwind CSS v4 Modern Standards & Enforcement

## Core Principle

Always write idiomatic, modern Tailwind CSS v4 utilities. Never use legacy v3 syntax or unnecessary arbitrary values when v4 provides first-class native utilities.

---

## Rules & Enforcement

### 1. Linear Gradients (`bg-linear-*` NOT `bg-gradient-*`)

Tailwind v4 replaces the legacy `bg-gradient-to-*` utilities with the modernized CSS linear gradient syntax `bg-linear-to-*`.

- ❌ **FORBIDDEN (Legacy v3 syntax)**:
  ```html
  <div class="bg-gradient-to-r from-blue-500 to-purple-600"></div>
  <div class="bg-gradient-to-t from-black via-zinc-900 to-transparent"></div>
  <div class="bg-gradient-to-b from-indigo-500 to-teal-400"></div>
  ```

- ✅ **REQUIRED (Tailwind v4 syntax)**:
  ```html
  <!-- Use bg-linear-to-* -->
  <div class="bg-linear-to-r from-blue-500 to-purple-600"></div>
  <div class="bg-linear-to-t from-black via-zinc-900 to-transparent"></div>
  <div class="bg-linear-to-b from-indigo-500 to-teal-400"></div>
  
  <!-- Angles and custom directions -->
  <div class="bg-linear-45 from-pink-500 to-rose-500"></div>
  ```

---

### 2. Native Z-Index Utilities (`z-1`, `z-2` NOT `z-[1]`, `z-[2]`)

Tailwind v4 includes a fine-grained, contiguous z-index scale. Do not use arbitrary value brackets `z-[...]` for small integers.

- ❌ **FORBIDDEN (Unnecessary arbitrary brackets)**:
  ```html
  <div class="z-[1]"></div>
  <div class="z-[2]"></div>
  <div class="z-[3]"></div>
  ```

- ✅ **REQUIRED (Native v4 scale)**:
  ```html
  <div class="z-0"></div>
  <div class="z-1"></div>
  <div class="z-2"></div>
  <div class="z-3"></div>
  <div class="z-10"></div>
  <div class="z-20"></div>
  <div class="z-50"></div>
  ```

---

### 3. Container & Layout Utilities

- Use `@container` and `@min-[...]` / `@max-[...]` container queries natively without third-party plugins.
- Use 3D transform utilities (`transform-3d`, `rotate-x-*`, `rotate-y-*`, `perspective-*`) natively supported in v4.

---

### 4. Summary Checklist for AI Agents

| Feature | Legacy / Disallowed (v3) | Required Modern Standard (v4) |
| :--- | :--- | :--- |
| **Gradients** | `bg-gradient-to-r`, `bg-gradient-to-b` | `bg-linear-to-r`, `bg-linear-to-b` |
| **Angled Gradients** | `bg-[linear-gradient(45deg,...)]` | `bg-linear-45`, `bg-linear-120` |
| **Low Z-Index** | `z-[1]`, `z-[2]`, `z-[3]` | `z-1`, `z-2`, `z-3` |
