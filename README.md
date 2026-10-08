# 🧠 AI Agent Skills Collection

A curated collection of production-grade skills and architectural playbooks for AI coding agents ([Antigravity](https://antigravity.google), [Claude Code](https://claude.ai), [Cursor](https://cursor.com), [GitHub Copilot](https://github.com/features/copilot), and [skills.sh](https://skills.sh)).

---

## 📦 Available Skills

| Skill | Category | Description | Quick Install |
| :--- | :--- | :--- | :--- |
| **[`modular-nextjs-lean`](./skills/modular-nextjs-lean)** | Architecture | Production-grade Lean Modular Architecture for Next.js landing pages, marketing sites, portfolios, and content websites. | `npx skills add AshutoshDM1/skills --skill modular-nextjs-lean` |
| **[`modular-nextjs-dynamic`](./skills/modular-nextjs-dynamic)** | Architecture | Dynamic Modular Architecture for Next.js SaaS applications, CRMs, interactive dashboards, and data-heavy portals. | `npx skills add AshutoshDM1/skills --skill modular-nextjs-dynamic` |

---

## 🚀 Installation Guide

### Install into a Project (Workspace Level)

To install a specific skill into your current project:

```bash
# Install Lean Modular Next.js Skill
npx skills add AshutoshDM1/skills --skill modular-nextjs-lean

# Install Dynamic Modular Next.js Skill
npx skills add AshutoshDM1/skills --skill modular-nextjs-dynamic
```

To install all skills in this repository at once:

```bash
npx skills add AshutoshDM1/skills
```

---

### Install Globally (Available Across All Projects)

To make a skill globally available to your AI agent across any folder:

```bash
npx skills add AshutoshDM1/skills --skill modular-nextjs-lean -g
```

---

## 📖 Skill Overviews

### 1. `modular-nextjs-lean`
Designed for **marketing websites, high-converting landing pages, documentation, brand portfolios, and legal pages**.
- **Thin App Router Shell (`src/app/`)**: `page.tsx` is an adapter (8–12 lines) with zero UI logic.
- **Direct Module Imports**: Clean imports directly from `@/modules/[Feature]/[Feature]`.
- **Flat Components vs. Folds Convention**: Simple pages stay flat; complex multi-section landing pages use PascalCase Folds (`src/modules/Home/Folds/`).
- **Decoupled SEO & Metadata**: Centralized metadata configs in `src/metadata/`.
- **Centralized Services**: Centralized API integrations in `src/services/`.

### 2. `modular-nextjs-dynamic`
Designed for **interactive SaaS web applications, customer portals, CRMs, and complex dashboards**.
- **Vertical Slice Module Encapsulation**: Colocated components, state hooks, types, and services per feature domain.
- **Module Self-Containment**: Complex domains contain their own sub-services, stores, and UI without bleeding into global scope.
- **Strict Data Access Layers**: Clean separation between server actions, client mutations, and data hooks.

---

## 🛠️ Repository Structure

```text
skills/
├── README.md                       # Catalog and documentation
├── LICENSE                         # MIT License
└── skills/                         # Skill definitions
    ├── modular-nextjs-lean/
    │   ├── SKILL.md                # Skill instructions & frontmatter
    │   └── references/             # Detailed reference guides
    │       ├── metadata.md
    │       ├── modules.md
    │       ├── services.md
    │       └── shared-ui.md
    └── modular-nextjs-dynamic/
        ├── SKILL.md                # Skill instructions & frontmatter
        └── references/             # Detailed reference guides
            ├── hooks-and-state.md
            ├── metadata.md
            ├── modules.md
            └── services.md
```

---

## 📄 License

This repository is licensed under the [MIT License](./LICENSE).
