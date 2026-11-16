---
name: better-modular-seo-structure
description: Architecture standards and scaffolding workflows for Next.js SEO & Metadata in src/metadata/. Enforces central site configuration, fail-fast env validation, decoupled per-route Metadata objects, and Schema.org JSON-LD structured data.
license: MIT
---

# Modular SEO & Metadata Architecture (`src/metadata/`)

This skill governs the configuration, scaffolding, and integration of the **Decoupled SEO & Metadata Architecture** inside `src/metadata/`.

---

## 1. Core Architectural Laws

1. **Decoupled from Routing**: `src/app/**/page.tsx` must never contain raw inline `Metadata` objects. All metadata configurations live cleanly inside `src/metadata/`.
2. **Fail-Fast Environment Validation**: `site.config.ts` must assert that `NEXT_PUBLIC_SITE_URL` exists at startup. Silent fallbacks (`process.env.URL || 'localhost'`) are strictly forbidden.
3. **Canonical Link Enforcement**: Every route must specify `alternates.canonical` pointing to its full canonical URL (`${siteConfig.url}/[route]`) to prevent Google duplicate content penalties.
4. **Standard 1200x630 Social Cards**: OpenGraph and Twitter images must specify explicit width (`1200`), height (`630`), and descriptive alt text.
5. **Machine-Readable Rich Snippets**: Provide Schema.org JSON-LD structured data (`Organization`, `WebSite`) inside `src/metadata/structuredData/`.

---

## 2. Directory Anatomy

```text
src/metadata/
├── site.config.ts          # Central source of truth & env validation
├── index.ts                # Master barrel export (single public entry point)
├── seo/                    # Route-specific Next.js Metadata objects
│   ├── root.ts             # Global root layout metadata
│   ├── home.ts             # Home page (canonical, OG, Twitter)
│   └── contact.ts          # Contact page metadata
└── structuredData/         # Schema.org JSON-LD definitions
    └── organization.ts     # Organization & WebSite schemas
```

---

## 3. Standard File Templates

### A. Central Site Configuration (`src/metadata/site.config.ts`)
```ts
const siteUrl = process.env.NEXT_PUBLIC_SITE_URL;
if (!siteUrl) {
  throw new Error("Missing required environment variable: NEXT_PUBLIC_SITE_URL");
}

export const siteConfig = {
  name: "MyBrand",
  shortName: "MyBrand",
  description: "Next-generation digital platform delivering high-performance web experiences.",
  ogDescription: "Digital platform delivering high-performance web experiences.",
  url: siteUrl,
  ogImage: "/og-image.webp",
  keywords: [
    "Next.js",
    "React",
    "Tailwind CSS",
    "Web Development",
    "Software Engineering",
  ],
  authors: [{ name: "Brand Team", url: siteUrl }],
  creator: "MyBrand",
  publisher: "MyBrand",
  social: {
    twitter: "@mybrand",
    email: "contact@mybrand.com",
    github: "https://github.com/mybrand",
  },
} as const;

export type SiteConfig = typeof siteConfig;
```

---

### B. Route-Specific SEO (`src/metadata/seo/home.ts`)
```ts
import type { Metadata } from "next";
import { siteConfig } from "../site.config";

export const homeMetadata: Metadata = {
  title: `${siteConfig.name} — High-Performance Digital Experiences`,
  description: siteConfig.description,
  alternates: {
    canonical: `${siteConfig.url}/`,
    languages: {
      en: `${siteConfig.url}/`,
      "en-US": `${siteConfig.url}/`,
      "x-default": `${siteConfig.url}/`,
    },
  },
  openGraph: {
    title: `${siteConfig.name} — High-Performance Digital Experiences`,
    description: siteConfig.ogDescription,
    url: `${siteConfig.url}/`,
    siteName: siteConfig.name,
    images: [
      {
        url: siteConfig.ogImage,
        width: 1200,
        height: 630,
        alt: `${siteConfig.name} Social Card`,
      },
    ],
    locale: "en_US",
    type: "website",
  },
  twitter: {
    card: "summary_large_image",
    site: "https://www.ashutoshtiwari.is-a.dev",
    creator: siteConfig.social.twitter,
    title: `${siteConfig.name} — High-Performance Digital Experiences`,
    description: siteConfig.ogDescription,
    images: [
      {
        url: siteConfig.ogImage,
        width: 1200,
        height: 630,
        alt: `${siteConfig.name} Social Card`,
      },
    ],
  },
};
```

---

### C. Structured Data (`src/metadata/structuredData/organization.ts`)
```ts
import { siteConfig } from "../site.config";

export const organizationJsonLd = {
  "@context": "https://schema.org",
  "@type": "Organization",
  name: siteConfig.name,
  alternateName: siteConfig.shortName,
  url: siteConfig.url,
  logo: `${siteConfig.url}${siteConfig.ogImage}`,
  description: siteConfig.description,
  sameAs: [
    `https://twitter.com/${siteConfig.social.twitter.replace("@", "")}`,
    siteConfig.social.github,
  ],
  contactPoint: {
    "@type": "ContactPoint",
    email: siteConfig.social.email,
    contactType: "customer service",
    availableLanguage: ["English"],
  },
};

export const websiteJsonLd = {
  "@context": "https://schema.org",
  "@type": "WebSite",
  name: siteConfig.name,
  url: siteConfig.url,
  description: siteConfig.description,
  publisher: {
    "@type": "Organization",
    name: siteConfig.name,
    logo: {
      "@type": "ImageObject",
      url: `${siteConfig.url}${siteConfig.ogImage}`,
    },
  },
};
```

---

### D. The Master Barrel (`src/metadata/index.ts`)
```ts
export * from "./site.config";
export * from "./seo/home";
export * from "./seo/root";
export * from "./structuredData/organization";
```

---

## 4. App Router Integration

Thin route adapters in `src/app/` bind the metadata object and inject structured data:

```tsx
// src/app/page.tsx
import { homeMetadata, organizationJsonLd, websiteJsonLd } from "@/metadata";
import { Home } from "@/modules/Home/Home";

export const metadata = homeMetadata;

export default function Page() {
  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(organizationJsonLd) }}
      />
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(websiteJsonLd) }}
      />
      <Home />
    </>
  );
}
```

---

## 5. Agent Invocation Workflows

When an agent is invoked with this skill:

1. **Check for `src/metadata/`**:
   - If missing: Scaffold `site.config.ts`, `seo/`, `structuredData/`, and `index.ts`.
   - Ask the user to confirm brand details (brand name, description, twitter handle).
2. **Adding a New Route**:
   - When creating a new route (e.g. `/contact`), create `src/metadata/seo/contact.ts`.
   - Re-export in `src/metadata/seo/index.ts`.
   - Bind `export const metadata = contactMetadata;` in `src/app/contact/page.tsx`.
3. **Auditing Existing Metadata**:
   - Scan for missing canonical links or un-sized OG images.
   - Propose refactoring inline `page.tsx` metadata into `src/metadata/`.
