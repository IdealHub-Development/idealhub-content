# free-tools SEO fixes

Handoff notes for the frontend team. Content-side fields are in place in this repository.
Each section lists what the renderer still has to consume.

**Mechanism:** the frontend renders JSON-LD straight from content. Proven live on
`/renovation` (`LocalBusiness` + `Service` from `renovation/en/seo.json`) and
`/renovation/faq` (`FAQPage` + `BreadcrumbList` from `seo.json.faq.structuredData`).
`free-tools/<locale>/defect-checklist/seo.json` now follows the same `structuredData`
convention.

---

## 1. Structured data — items 2 & 5  ✅ content complete

**Was:** page emitted only the auto-generated `Organization` + `WebPage` blocks. No
`FAQPage`, no `BreadcrumbList`, no `datePublished`/`dateModified`.

**Now:** `seo.json.structuredData` holds a `@graph` with three entities per locale:

| Entity | Purpose |
|---|---|
| `WebPage` | Carries `name`, `description`, `inLanguage`, `datePublished` (2026-02-01), `dateModified` (2026-10-12) |
| `FAQPage` | 5 Q&As targeting the DLP / vacant-possession query cluster → FAQ rich results |
| `BreadcrumbList` | 2 levels: `Home > Property Defect Checklist` |

Breadcrumb is deliberately **2 levels** — `/free-tools/` returns **404**, so a
"Free Tools" tier was omitted rather than point at a dead URL.

**Renderer requirement:** emit `seo.json.structuredData` verbatim in a
`<script type="application/ld+json">` block.

> ⚠️ **Watch for a duplicate `WebPage`.** The page currently auto-generates a `WebPage`
> block from `hero.md`. If the renderer emits `structuredData` *in addition to* the auto
> block, two `WebPage` entities will appear. Ideally `structuredData` **replaces** the auto
> block. Both now carry matching `@id` values
> (`...#webpage`) to aid merging, but confirm which behaviour the renderer uses.

---

## 2. `&apos;` HTML entity in JSON-LD — item 3  ✅ content complete

**Was:** the auto-generated `WebPage` description contained a literal `&apos;`
(`Don&apos;t let hidden defects...`). HTML entities are **not** decoded inside
`<script type="application/ld+json">` — script content is raw text — so consumers
received the entity as literal characters.

**Root cause:** `hero.md` description contained a straight apostrophe which the renderer
HTML-escaped when injecting it into JSON.

**Fixed two ways:**
1. `hero.md` description rewritten to avoid the apostrophe entirely
   ("Stop hidden defects from draining your savings. ...").
2. All `structuredData` strings are authored **apostrophe-free** (audited: 0 straight
   quotes, 0 HTML entities across all three locales), so no escaping can reintroduce one.

---

## 3. OG image — item 4  ✅ content complete

**Was:** `image` pointed at `/thumbnails/Idealhub-thumbnail.webp` — a generic site-wide
asset, **450 × 800 (portrait 9:16)**, which social platforms crop or letterbox badly.

**Now:** `image` points at `/free-tools/hero-illustration.webp` — page-specific,
**1280 × 1120**, verified HTTP 200 `image/webp`.

> Note: `renovation` also uses one shared image across all locales
> (`renovation/images/home/renovation-thumbnail.webp` for en/ms/zh), so per-locale OG
> images are **not** this repo's convention — page-specific is the goal here.
> A purpose-made **1200 × 630** crop would be ideal; no suitable asset exists yet.

---

## 4. Image alt text — item 6  ✅ content complete

**Problem (live):** the hero image is rendered twice with the **same generic alt**, and no
`og:image:alt` is emitted.

| Placement | Live `alt` | Should be |
|---|---|---|
| Full-bleed background layer | `Property defect inspection illustration` | `""` (decorative) |
| Visible hero image | `Property defect inspection illustration` | descriptive, locale-specific |

**Content available** — `hero.md` (en / ms / zh):

```yaml
image: "/free-tools/hero-illustration.webp"
alt: "Malaysian homeowner inspecting wall tiles and floor surfaces for defects during a vacant possession handover"
backgroundAlt: ""   # decorative full-bleed layer -> empty alt
```

`seo.json.imageAlt` supplies the `og:image:alt` text.

Field naming follows the gallery convention in
`renovation/<locale>/case-studies/<slug>/index.md` (each image paired with an `alt:` key).

**Renderer requirement:**

```jsx
// Background layer: decorative, hidden from assistive tech
<img
  src={hero.image}
  alt={hero.backgroundAlt}   // ""
  aria-hidden="true"
  className="tw:absolute tw:inset-0 tw:size-full tw:object-cover"
/>

// Visible hero image: descriptive, locale-specific
<img
  src={hero.image}
  alt={hero.alt}
  className="tw:max-h-[480px] tw:w-auto tw:rounded-2xl tw:object-cover"
/>

// <head>
<meta property="og:image:alt" content={seo.imageAlt} />
<meta name="twitter:image:alt" content={seo.imageAlt} />
```

The footer payment-gateway logos (`class="ZzWTK"`, 10 images, `alt=""`) belong to a shared
footer component and are out of scope.

---

## 5. Not fixable from this repository

**URL is in no XML sitemap** — the highest-impact issue. Checked all five child sitemaps
under `idealhub-rental-media.s3.ap-southeast-5.amazonaws.com/sitemap/`; none contains
`/free-tools/` or `defect`. Needs an entry (with `lastmod`) added to the sitemap in S3.

---

## Verification after deploy

Fetch `https://www.idealhub.com/free-tools/property-defect-checklist` and confirm:

1. JSON-LD includes `FAQPage` and `BreadcrumbList`; **exactly one** `WebPage`.
2. No `&apos;` anywhere inside `<script type="application/ld+json">`.
3. `datePublished` and `dateModified` present.
4. `og:image` is `/free-tools/hero-illustration.webp`; `og:image:alt` present.
5. One hero `<img>` has the descriptive alt; the background copy has `alt=""`.
6. Alt text differs per locale (`?lng=ms-MY`, `?lng=zh-CN`).
7. Validate at <https://search.google.com/test/rich-results> — FAQ and breadcrumb eligible.
