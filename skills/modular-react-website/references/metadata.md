# Metadata & SEO Architecture Guide (React / Vite)

This guide defines how SEO metadata and Schema.org JSON-LD structured data are organized under `src/metadata/` in React applications.

---

## 1. Directory Structure

```text
src/metadata/
├── site.config.ts        # Global site-wide constants (URL, author, default OG image, social handles)
├── types.ts              # PageMetadata TypeScript interface
├── seo/                  # Page-specific SEO & bespoke OpenGraph/Twitter configurations
│   ├── root.ts           # Root default metadata & title template
│   ├── home.ts           # Home page metadata
│   ├── contact.ts        # Contact page metadata
│   ├── terms.ts
│   ├── privacy.ts
│   └── index.ts
├── structuredData/       # Schema.org JSON-LD structured data generators
│   ├── organization.ts   # Organization & WebSite JSON-LD schemas
│   ├── breadcrumbs.ts    # BreadcrumbList generator
│   └── index.ts
└── index.ts              # Root metadata barrel export
```

---

## 2. Page Metadata Interface (`src/metadata/types.ts`)

```ts
export interface OpenGraphMetadata {
  title: string;
  description: string;
  url: string;
  siteName: string;
  images: Array<{
    url: string;
    width: number;
    height: number;
    alt: string;
  }>;
  type: 'website' | 'article';
}

export interface TwitterMetadata {
  card: 'summary' | 'summary_large_image';
  title: string;
  description: string;
  images: string[];
  creator: string;
}

export interface PageMetadata {
  title: string;
  description: string;
  canonical?: string;
  openGraph?: OpenGraphMetadata;
  twitter?: TwitterMetadata;
  structuredData?: Record<string, unknown> | Array<Record<string, unknown>>;
}
```

---

## 3. SEO Blueprint Example (`src/metadata/seo/contact.ts`)

```ts
import { siteConfig } from '../site.config';
import type { PageMetadata } from '../types';

export const contactMetadata: PageMetadata = {
  title: `Contact Us | ${siteConfig.name}`,
  description:
    'Get in touch with our team to discuss your project, explore collaborations, or answer any technical inquiries.',
  canonical: `${siteConfig.url}/contact`,
  openGraph: {
    title: `Contact ${siteConfig.name} — Start Your Next Project`,
    description: "Get in touch with our team. Let's discuss your next digital engineering project.",
    url: `${siteConfig.url}/contact`,
    siteName: siteConfig.name,
    images: [
      {
        url: `${siteConfig.url}/images/og-contact.png`,
        width: 1200,
        height: 630,
        alt: `Contact ${siteConfig.name}`,
      },
    ],
    type: 'website',
  },
  twitter: {
    card: 'summary_large_image',
    title: `Contact ${siteConfig.name}`,
    description: "Get in touch with our team. Let's discuss your next project.",
    images: [`${siteConfig.url}/images/og-contact.png`],
    creator: siteConfig.twitterHandle,
  },
};
```

---

## 4. Shared PageHead Adapter Component (`src/shared/PageHead/PageHead.tsx`)

Using `react-helmet-async` (or document update hook):

```tsx
import { Helmet } from 'react-helmet-async';
import type { PageMetadata } from '@/metadata/types';

export function PageHead({
  title,
  description,
  canonical,
  openGraph,
  twitter,
  structuredData,
}: PageMetadata) {
  return (
    <Helmet>
      <title>{title}</title>
      <meta name="description" content={description} />
      {canonical && <link rel="canonical" href={canonical} />}

      {/* OpenGraph */}
      {openGraph && (
        <>
          <meta property="og:title" content={openGraph.title} />
          <meta property="og:description" content={openGraph.description} />
          <meta property="og:url" content={openGraph.url} />
          <meta property="og:site_name" content={openGraph.siteName} />
          <meta property="og:type" content={openGraph.type} />
          {openGraph.images.map((img) => (
            <meta key={img.url} property="og:image" content={img.url} />
          ))}
        </>
      )}

      {/* Twitter */}
      {twitter && (
        <>
          <meta name="twitter:card" content={twitter.card} />
          <meta name="twitter:title" content={twitter.title} />
          <meta name="twitter:description" content={twitter.description} />
          <meta name="twitter:creator" content={twitter.creator} />
          {twitter.images.map((img) => (
            <meta key={img} name="twitter:image" content={img} />
          ))}
        </>
      )}

      {/* JSON-LD Structured Data */}
      {structuredData && (
        <script type="application/ld+json">
          {JSON.stringify(structuredData)}
        </script>
      )}
    </Helmet>
  );
}
```
