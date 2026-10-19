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

---

## Agent Enforcement Workflow

When generating, refactoring, or reviewing code:
1. **Discover & Load**: Read and reference the applicable rule specifications from the subdirectories (e.g. [`strict-env/strict-env.md`](./strict-env/strict-env.md)).
2. **Apply Constraints**: Strictly adhere to the policies defined in each active rule module.
3. **Fail Fast**: Never introduce silent fallback defaults or bypass security boundaries.
