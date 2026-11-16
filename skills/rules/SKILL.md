---
name: rules
description: Curated collection of essential coding rules, strict environment policies, security boundaries, and architectural standards for AI coding agents.
license: MIT
---

# Project Guidelines & Rules Collection

This skill coordinates and enforces project-wide coding rules, security boundaries, and architectural standards. Detailed rule specifications are modularized in their respective subdirectories within this skill package.

## Rule Modules

- **[`strict-env`](./strict-env/strict-env.md)**: Fail-fast policy for environment variables. Enforces zero silent fallbacks (`process.env.VAR || 'default'` is strictly forbidden), explicit runtime assertions, centralized validation (Zod / T3 Env), and strict client vs. server secret isolation.
- **[`tailwind-v4`](./tailwind-v4/tailwind-v4.md)**: Modern Tailwind CSS v4 standards. Enforces `bg-linear-*` over legacy `bg-gradient-*`, native z-index utilities (`z-1`, `z-2` over `z-[1]`, `z-[2]`), and clean modern syntax.
- **[`typography`](./typography/typography.md)**: Sizing hierarchy and readability standards. Forbids overusing tiny `text-xs` for primary UI content; enforces confident sizing with `base + sm` and responsive pairs like `text-sm md:text-base` and `text-xs md:text-sm`.
- **[`barrel-policy`](./barrel-policy/barrel-policy.md)**: Strict barrel re-export standards. Whitelists only the 6 allowed `index.ts` files (`src/shared`, `Folds`, `Icons`, compound components, `src/metadata`, `src/services`); strictly bans barrels in `components/ui`, root feature modules, props-based components, and inner domain service folders to prevent memory bloat and private leaks.
- **[`thin-route-adapters`](./thin-route-adapters/thin-route-adapters.md)**: Thin route adapter law. Restricts `src/app/**/page.tsx` to thin (under 15 lines) wrappers delegating to PascalCase feature modules in `src/modules/`; forbids inline layout JSX, state hooks, and cross-module imports.
- **[`props-based-architecture`](./props-based-architecture/props-based-architecture.md)**: Component archetype and anti-microfile rules. Enforces the 80–90% props-based default, single-file threshold (< 300 lines) to prevent file sprawl, and zero `index.ts` leaks for multi-file component decomposition.

---

## Agent Enforcement Workflow

When generating, refactoring, or reviewing code:
1. **Discover & Load**: Read and reference the applicable rule specifications from the subdirectories (e.g. [`strict-env/strict-env.md`](./strict-env/strict-env.md), [`typography/typography.md`](./typography/typography.md)).
2. **Apply Constraints**: Strictly adhere to the policies defined in each active rule module.
3. **Fail Fast**: Never introduce silent fallback defaults, microscopic illegible typography, or bypass security boundaries.
