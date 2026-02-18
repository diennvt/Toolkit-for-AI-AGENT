---
name: SEO
description: Search Engine Optimization - meta tags, structured data, sitemap, Core Web Vitals, SSR/SSG
---

# SEO (Search Engine Optimization)

## Tổng quan

Skill kit này hướng dẫn tối ưu SEO cho web app: từ meta tags, structured data, sitemap, đến Core Web Vitals và chiến lược rendering (SSR/SSG). Giúp trang web xuất hiện tốt trên Google và các search engine.

**Khi nào sử dụng:** Khi build web app cần được tìm thấy qua tìm kiếm (landing pages, marketing sites, e-commerce, blogs).

---

## Checklist

- [ ] Meta tags chuẩn cho mỗi page (title, description, og:*)
- [ ] Heading hierarchy đúng (1 h1/page, h2 → h3 logic)
- [ ] Semantic HTML (header, nav, main, article, footer)
- [ ] Sitemap.xml tự động
- [ ] Robots.txt cấu hình đúng
- [ ] Canonical URLs tránh duplicate content
- [ ] Structured data (JSON-LD) cho content chính
- [ ] Core Web Vitals đạt chuẩn (LCP, FID, CLS)
- [ ] Mobile-friendly (responsive)
- [ ] HTTPS
- [ ] Image optimization (alt text, lazy loading, WebP)
- [ ] URL structure rõ ràng, readable
- [ ] Chọn rendering strategy (SSR / SSG / CSR)

---

## Hướng dẫn chi tiết

### Meta Tags Template

```html
<head>
  <!-- Basic -->
  <title>Product Name - Page Title | Brand</title>
  <meta name="description" content="Clear, compelling description under 160 chars.">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="canonical" href="https://example.com/page">
  
  <!-- Open Graph (Facebook, LinkedIn) -->
  <meta property="og:title" content="Page Title">
  <meta property="og:description" content="Description for social sharing">
  <meta property="og:image" content="https://example.com/og-image.jpg">
  <meta property="og:url" content="https://example.com/page">
  <meta property="og:type" content="website">
  
  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="Page Title">
  <meta name="twitter:description" content="Description for Twitter">
  <meta name="twitter:image" content="https://example.com/twitter-image.jpg">
</head>
```

### Next.js SEO (App Router)

```typescript
// app/products/[id]/page.tsx
import { Metadata } from 'next';

// Dynamic metadata per page
export async function generateMetadata({ params }): Promise<Metadata> {
  const product = await getProduct(params.id);
  
  return {
    title: `${product.name} | MyShop`,
    description: product.description.slice(0, 160),
    openGraph: {
      title: product.name,
      description: product.description,
      images: [product.imageUrl],
    },
    alternates: {
      canonical: `https://myshop.com/products/${params.id}`,
    },
  };
}

// app/layout.tsx — Global defaults
export const metadata: Metadata = {
  metadataBase: new URL('https://myshop.com'),
  title: {
    default: 'MyShop - Best Online Store',
    template: '%s | MyShop',
  },
  description: 'Shop the best products online',
  robots: { index: true, follow: true },
};
```

### Structured Data (JSON-LD)

```html
<!-- Product page -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "Product Name",
  "description": "Product description",
  "image": "https://example.com/product.jpg",
  "offers": {
    "@type": "Offer",
    "price": "29.99",
    "priceCurrency": "USD",
    "availability": "https://schema.org/InStock"
  },
  "aggregateRating": {
    "@type": "AggregateRating",
    "ratingValue": "4.5",
    "reviewCount": "120"
  }
}
</script>

<!-- Article/Blog page -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Article Title",
  "author": { "@type": "Person", "name": "Author Name" },
  "datePublished": "2025-01-15",
  "dateModified": "2025-01-20",
  "image": "https://example.com/article-image.jpg"
}
</script>
```

### Sitemap.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://example.com/</loc>
    <lastmod>2025-01-15</lastmod>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://example.com/products</loc>
    <lastmod>2025-01-14</lastmod>
    <priority>0.8</priority>
  </url>
</urlset>
```

```typescript
// Next.js — Auto-generate sitemap
// app/sitemap.ts
export default async function sitemap() {
  const products = await getProducts();
  
  const productUrls = products.map((product) => ({
    url: `https://myshop.com/products/${product.id}`,
    lastModified: product.updatedAt,
    priority: 0.8,
  }));

  return [
    { url: 'https://myshop.com', lastModified: new Date(), priority: 1.0 },
    { url: 'https://myshop.com/products', lastModified: new Date(), priority: 0.9 },
    ...productUrls,
  ];
}
```

### Robots.txt

```
# robots.txt
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/
Disallow: /private/

Sitemap: https://example.com/sitemap.xml
```

### Core Web Vitals

```
┌─────────────────────────────────────────────┐
│ Metric  │ Tốt    │ Cần cải thiện │ Kém    │
├─────────┼────────┼───────────────┼────────┤
│ LCP     │ ≤2.5s  │ 2.5-4s        │ >4s    │
│ FID     │ ≤100ms │ 100-300ms     │ >300ms │
│ CLS     │ ≤0.1   │ 0.1-0.25      │ >0.25  │
└─────────────────────────────────────────────┘

LCP = Largest Contentful Paint (tốc độ load content chính)
FID = First Input Delay (thời gian phản hồi click đầu tiên)
CLS = Cumulative Layout Shift (độ giật layout)
```

### Image Optimization

```html
<!-- ✅ GOOD -->
<img
  src="/images/product.webp"
  alt="Red running shoe, side view"
  width="600"
  height="400"
  loading="lazy"
  decoding="async"
/>

<!-- Next.js Image -->
<Image
  src="/images/product.webp"
  alt="Red running shoe, side view"
  width={600}
  height={400}
  priority={isAboveFold}
/>

<!-- ❌ BAD -->
<img src="/images/product.png">  <!-- No alt, no dimensions, no lazy -->
```

### Rendering Strategy

| Strategy | SEO | Speed | Khi nào dùng |
|----------|-----|-------|-------------|
| **SSG** (Static Site Generation) | ✅ Tốt nhất | ✅ Nhanh nhất | Blog, docs, marketing pages |
| **SSR** (Server-Side Rendering) | ✅ Tốt | 🟡 Tốt | E-commerce, dynamic content cần SEO |
| **CSR** (Client-Side Rendering) | ❌ Kém | 🟡 Tùy | Dashboards, admin panels (không cần SEO) |
| **ISR** (Incremental Static) | ✅ Tốt | ✅ Nhanh | Content thay đổi thường xuyên |

---

## SEO Checklist tối thiểu

```
□ Mỗi page có unique <title> và <meta description>
□ 1 <h1> duy nhất mỗi page
□ Tất cả images có alt text
□ URL readable (/products/red-shoe thay vì /p?id=123)
□ Sitemap.xml tồn tại
□ Robots.txt cấu hình đúng
□ HTTPS enabled
□ Mobile responsive
□ Page load < 3 giây
□ Không có broken links (404)
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Cùng title/description cho mọi page | Unique title + description mỗi page |
| Dùng CSR cho trang cần SEO | Dùng SSR/SSG cho public pages |
| Quên alt text cho images | Mọi `<img>` cần alt mô tả nội dung |
| URL chứa query params xấu | URL readable, slug-based |
| Không có sitemap | Auto-generate sitemap.xml |
| Duplicate content không có canonical | Thêm `<link rel="canonical">` |
| Ảnh quá nặng (5MB PNG) | Dùng WebP, lazy loading, resize |

---

## Liên kết

- **Process liên quan:** [05-Implementation](../../processes/05-implementation/SKILL.md)
- **Skill liên quan:** [frontend-development](../frontend-development/SKILL.md), [performance-optimization](../performance-optimization/SKILL.md)
