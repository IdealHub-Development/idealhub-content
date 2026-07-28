# SEO Analysis — IdealHub Renovation Pages

**Date:** 2026-07-28
**URL:** https://rental.idealhub.duckdns.org/renovation

---

## 1. `/renovation` — Index Page

### Current State

| Element | Status | Value |
|---------|--------|-------|
| **`<title>`** | ✅ | Turnkey Renovation Service in Klang Valley & Kuala Lumpur — High-Yield Rental Interiors \| IdealHub |
| **`<meta name="description">`** | ✅ | Keyword-rich with Klang Valley, KL, Puchong, Ampang, Cyberjaya, Sungai Long + renovation contractor + apartment/condo |
| **`og:title`** | ✅ | Matches title |
| **`og:description`** | ✅ | Matches meta description |
| **`og:url`** | ✅ | `https://rental.idealhub.duckdns.org/renovation` |
| **`og:type`** | ❌ | `Product` — should be `website` |
| **`og:image`** | ⚠️ | `renovation-thumbnail.jpeg` — should be `.webp` |
| **`twitter:card`** | ⚠️ | `photo` — should be `summary_large_image` |
| **`twitter:title`** | ✅ | Matches |
| **`twitter:description`** | ✅ | Matches |
| **`twitter:image`** | ⚠️ | Same `.jpeg` |
| **Viewport** | ✅ | `width=device-width, initial-scale=1` |
| **Charset** | ✅ | `utf-8` |
| **GTM** | ✅ | `GTM-5R4X6H6` present |
| **Manifest** | ✅ | `/manifest.json` |
| **Apple Touch Icon** | ✅ | Present |
| **Canonical URL** | ❌ | **Missing** |
| **Robots meta** | ❌ | **Missing** |
| **Hreflang** | ❌ | **Missing** |
| **JSON-LD count** | ⚠️ 1 | Only `LocalBusiness` — `Service` schema not rendered |

### JSON-LD Rendered

```json
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "IdealHub",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "23-2, Jalan Radin Bagus 3, Bandar Baru Sri Petaling",
    "addressLocality": "Kuala Lumpur",
    "postalCode": "57000",
    "addressCountry": "MY"
  },
  "priceRange": "RM 15k - 70k",
  "sameAs": ["facebook", "instagram", "linkedin"]
}
```

---

## 2. `/renovation/case-study/<slug>` — Case Study Pages

### Current State

| Element | Status | Value |
|---------|--------|-------|
| **`<title>`** | ✅ | Per-case-study from `case-studies/<slug>/seo.json` |
| **`<meta name="description">`** | ✅ | Keyword-rich, location-specific |
| **`og:image`** | ✅ | WebP from GitHub raw |
| **`og:type`** | ❌ | `Product` — should be `article` |
| **Canonical URL** | ✅ | Found |
| **JSON-LD count** | ✅ 2 | `Article` + `BreadcrumbList` |

### JSON-LD Rendered (Example: plaza-rah)

**Block 1 — Article:**
```json
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Plaza Rah Apartment Refurbishment — Modern Contemporary Renovation in Kampung Baru, KL",
  "description": "A complete cosmetic and functional refurbishment...",
  "image": "https://...after.webp",
  "datePublished": "2026-07-27",
  "author": { "@type": "Organization", "name": "IdealHub" },
  "publisher": { "@type": "Organization", "name": "IdealHub" }
}
```

**Block 2 — BreadcrumbList:**
```json
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    { "@type": "ListItem", "position": 1, "name": "Home" },
    ...
  ]
}
```

---

## 3. Content-Side Fixes Needed

### 3.1 OG Image — Convert to WebP
- **File:** `seo.json`
- **Current:** OG image served as `renovation-thumbnail.jpeg`
- **Fix:** Replace with WebP version once available on the frontend CDN

### 3.2 Service Schema — Not Rendered
- **File:** `seo.json` (already has `servicePage` key with schema)
- **Issue:** Frontend reads `seo.json.structuredData` but `Service` schema is under `servicePage` key
- **Fix:** Frontend needs to read `seo.json.servicePage` and render as second `<script type="application/ld+json">` block on `/renovation` page

### 3.3 FAQPage Schema — Not Rendered
- **File:** `seo.json` (already has `faq.structuredData` with 8 Q&A pairs)
- **Issue:** Frontend doesn't render this schema on `/renovation/faq`
- **Fix:** Frontend needs to render `seo.json.faq.structuredData` on the FAQ page

---

## 4. Frontend-Side Fixes Needed

| # | Issue | Priority | Impact |
|---|-------|----------|--------|
| A | Fix `og:type` from `Product` to `website` (index) / `article` (case studies) | High | Incorrect rich result type |
| B | Render `Service` JSON-LD on `/renovation` | High | Service rich results |
| C | Render `FAQPage` JSON-LD on `/renovation/faq` | High | FAQ rich results (expandable Q&A in SERP) |
| D | Add `<link rel="canonical">` to `/renovation` and all pages | Medium | Prevent duplicate content penalty |
| E | Change `twitter:card` from `photo` to `summary_large_image` | Medium | Larger Twitter cards |
| F | Add `<meta name="robots" content="index, follow">` | Low | Explicit indexing signal |
| G | Add hreflang tags if multi-language support planned | Low | International SEO |

---

## 5. Content Data Sources

| Schema | Source File | Key | Rendered? |
|--------|------------|-----|-----------|
| `LocalBusiness` | `renovation/seo.json` | `structuredData` | ✅ Yes |
| `Service` | `renovation/seo.json` | `servicePage` | ❌ No (frontend) |
| `FAQPage` | `renovation/seo.json` | `faq.structuredData` | ❌ No (frontend) |
| `Article` (per case study) | `case-studies/<slug>/seo.json` | `structuredData` | ✅ Yes |
| `BreadcrumbList` | Frontend-generated | N/A | ✅ Yes |

---

## 6. Keyword Coverage — Klang Valley Searches

| Search Query | Title | Meta | H1 | Body |
|---|---|---|---|---|
| "renovation service klang valley" | ✅ | ✅ | ❌ | ❌ |
| "turnkey renovation kuala lumpur" | ✅ | ✅ | ❌ | ❌ |
| "apartment renovation kl" | ✅ (condo) | ✅ | ❌ | ✅ |
| "renovation contractor klang valley" | ❌ (no "contractor" in title) | ✅ | ❌ | ❌ |
| "rental renovation malaysia" | ✅ (high-yield rental) | ✅ | ❌ | ✅ |
| "home renovation puchong" | ❌ | ✅ (listed) | ❌ | ✅ (portfolio) |

---

## 7. Checklist — Completed

- ✅ Case study `index.md` files — frontmatter fixed, gallery `file:` refs, alt text
- ✅ Per-case-study `seo.json` — title, description, Article JSON-LD
- ✅ Root `seo.json` — `LocalBusiness` schema, page-level meta for all 4 pages
- ✅ All images converted from JPG/PNG → WebP
- ✅ Section image references updated (`.png` → `.webp`)
- ✅ FAQPage schema added (8 Q&A pairs)
- ✅ Service schema added with price range and areaServed
- ✅ Portfolio card yield uplift values (footerText)
- ✅ Index page meta title + description updated with Klang Valley keywords
- ✅ Gallery/portfolio page meta updated with location-specific areas
