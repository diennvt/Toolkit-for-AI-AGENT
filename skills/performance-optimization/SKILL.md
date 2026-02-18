---
name: Performance Optimization
description: Tối ưu hiệu suất - caching, lazy loading, query optimization, profiling
---

# Performance Optimization (Tối ưu hiệu suất)

## Tổng quan

Skill kit này hướng dẫn tối ưu hiệu suất ứng dụng: database query optimization, frontend performance, caching strategies, và profiling.

---

## Checklist

- [ ] Identify performance bottlenecks (profiling)
- [ ] Optimize database queries (N+1, indexing)
- [ ] Implement caching strategy
- [ ] Frontend: lazy loading, code splitting
- [ ] Optimize images và assets
- [ ] Implement pagination cho large datasets
- [ ] Minimize API response payload
- [ ] Connection pooling cho database
- [ ] CDN cho static assets

---

## Hướng dẫn chi tiết

### Database Optimization

```sql
-- ❌ N+1 Problem
-- 1 query lấy users + N queries lấy orders cho mỗi user
SELECT * FROM users;
-- For each user: SELECT * FROM orders WHERE user_id = ?

-- ✅ Fix: JOIN hoặc eager loading
SELECT u.*, o.* FROM users u
LEFT JOIN orders o ON u.id = o.user_id;

-- ✅ Hoặc: IN query
SELECT * FROM orders WHERE user_id IN (?, ?, ?);

-- ✅ Add indexes
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- ✅ Select only needed columns
SELECT id, name, email FROM users;   -- ✅
SELECT * FROM users;                  -- ❌ (loads all columns)

-- ✅ Pagination
SELECT * FROM products ORDER BY created_at DESC LIMIT 20 OFFSET 0;

-- ✅ EXPLAIN to analyze query
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'test@test.com';
```

### Frontend Performance

```typescript
// ✅ Lazy loading components
const Dashboard = React.lazy(() => import('./pages/Dashboard'));

// ✅ Image lazy loading
<img src="photo.jpg" loading="lazy" alt="..." />

// ✅ Debounce search input
const debouncedSearch = useMemo(
  () => debounce((query) => fetchResults(query), 300),
  []
);

// ✅ Memoize expensive computations
const sortedItems = useMemo(() => {
  return items.sort((a, b) => a.price - b.price);
}, [items]);

// ✅ Virtual scrolling for long lists
// Use react-virtualized or @tanstack/react-virtual

// ✅ Code splitting by route
const routes = [
  { path: '/', component: lazy(() => import('./Home')) },
  { path: '/products', component: lazy(() => import('./Products')) },
];
```

### Performance Metrics

| Metric | Target | Tool |
|--------|--------|------|
| **FCP** (First Contentful Paint) | < 1.8s | Lighthouse |
| **LCP** (Largest Contentful Paint) | < 2.5s | Lighthouse |
| **CLS** (Cumulative Layout Shift) | < 0.1 | Lighthouse |
| **API Response Time** | < 200ms (p95) | APM tools |
| **Database Query** | < 50ms | Query logs |
| **Bundle Size** | < 200KB (gzipped) | Webpack analyzer |

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Premature optimization | Profile first, optimize bottlenecks |
| SELECT * FROM ... | Select only needed columns |
| No pagination | Paginate all list endpoints |
| No image optimization | Compress, resize, use WebP |
| Synchronous blocking operations | Async/await, worker threads |
| No caching | Cache frequently accessed data |

---

## Liên kết

- **Skill liên quan:** [caching](../caching/SKILL.md), [database-design](../database-design/SKILL.md)
