# Metadata, SEO & Structured Data Architecture Guide

This guide defines how SEO metadata and Schema.org JSON-LD structured data are organized under `src/metadata/` in Next.js applications.

---

## 1. Directory Structure

```text
src/metadata/
├── site.config.ts        # Global site-wide constants (URL, author, default OG image, social handles)
├── seo/                  # Page-specific SEO & bespoke OpenGraph/Twitter configurations
│   ├── root.ts           # Root layout metadata & title template
│   ├── home.ts           # Home page metadata
│   ├── contact.ts        # Contact page metadata
│   ├── termsAndConditions.ts
│   ├── privacyPolicy.ts
│   ├── cookiePolicy.ts
│   └── index.ts
├── structuredData/       # Schema.org JSON-LD structured data generators
│   ├── organization.ts   # Organization & WebSite JSON-LD schemas
│   ├── breadcrumbs.ts    # BreadcrumbList generator
│   └── index.ts
└── index.ts              # Root metadata barrel export
```

---

## 2. Character Length Guidelines

- **Page Meta Description (SERP)**: **< 160 characters** (ideal: 140–155 chars) to prevent search engine truncation.
- **OpenGraph & Twitter Card Descriptions**: **70–90 characters** for punchy, high-impact social preview cards.
- **Bespoke Social Cards**: Each page defines its own hardcoded, custom OpenGraph and Twitter card objects tailored specifically for that page's context.

---

## 3. SEO Blueprint (`src/metadata/seo/contact.ts`)

```ts
import type { Metadata } from 'next';
import { siteConfig } from '../site.config';

export const contactMetadata: Metadata = {
  title: 'Contact Us | OpnixLabs',
  description:
    'Get in touch with the OpnixLabs team to discuss your project, explore collaborations, or answer any technical inquiries.',
  alternates: {
    canonical: `${siteConfig.url}/contact`,
    languages: {
      en: `${siteConfig.url}/contact`,
      'en-US': `${siteConfig.url}/contact`,
      'x-default': `${siteConfig.url}/contact`,
    },
  },
  openGraph: {
    title: 'Contact OpnixLabs — Start Your Next Project',
    description:
      "Get in touch with OpnixLabs. Let's discuss your next digital engineering project.",
    url: `${siteConfig.url}/contact`,
    siteName: 'OpnixLabs',
    images: [
      {
        url: '/og-image.webp',
        width: 1200,
        height: 630,
        alt: 'Contact OpnixLabs',
      },
    ],
    locale: 'en_US',
    type: 'website',
  },
  twitter: {
    card: 'summary_large_image',
    site: '@opnixlabs',
    creator: '@opnixlabs',
    title: 'Contact OpnixLabs — Start Your Next Project',
    description:
      "Get in touch with OpnixLabs. Let's discuss your next digital engineering project.",
    images: [
      {
        url: '/og-image.webp',
        width: 1200,
        height: 630,
        alt: 'Contact OpnixLabs',
      },
    ],
  },
};
```

---

## 4. Structured Data Blueprint (`src/metadata/structuredData/organization.ts`)

```ts
import { siteConfig } from '../site.config';

export const organizationJsonLd = {
  '@context': 'https://schema.org',
  '@type': 'Organization',
  name: siteConfig.name,
  alternateName: siteConfig.shortName,
  url: siteConfig.url,
  logo: `${siteConfig.url}${siteConfig.ogImage}`,
  description: siteConfig.description,
  sameAs: [
    'https://twitter.com/opnixlabs',
    'https://linkedin.com/company/opnixlabs',
    'https://github.com/OpnixLabs',
  ],
  contactPoint: {
    '@type': 'ContactPoint',
    email: siteConfig.social.email,
    contactType: 'customer service',
    availableLanguage: ['English'],
  },
};
```

---

## 5. Usage in Route Adapters

```tsx
// src/app/contact/page.tsx
import type { Metadata } from 'next';
import Contact from '@/modules/Contact/Contact';
import { contactMetadata } from '@/metadata';

export const metadata: Metadata = contactMetadata;

export default function ContactPage() {
  return <Contact />;
}
```
