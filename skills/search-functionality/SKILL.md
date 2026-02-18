---
name: Search Functionality
description: Search - full-text search, Elasticsearch, Algolia, search indexing, autocomplete
---

# Search Functionality (Tìm kiếm)

## Tổng quan

Skill kit này hướng dẫn implement tính năng tìm kiếm: từ database search cơ bản đến full-text search với Elasticsearch.

---

## Checklist

- [ ] Xác định search requirements (basic / full-text / advanced)
- [ ] Implement database-level search
- [ ] Implement search API endpoint
- [ ] Search debouncing (frontend)
- [ ] Search highlighting
- [ ] Autocomplete / suggestions
- [ ] Setup Elasticsearch (nếu cần advanced search)

---

## Hướng dẫn chi tiết

### Level 1: Database Search (SQL LIKE/ILIKE)

```sql
-- Basic search — đủ cho dự án nhỏ
SELECT * FROM products
WHERE name ILIKE '%iphone%' OR description ILIKE '%iphone%'
ORDER BY created_at DESC
LIMIT 20 OFFSET 0;

-- PostgreSQL Full-Text Search — tốt hơn LIKE
SELECT *, ts_rank(search_vector, query) AS rank
FROM products,
     to_tsquery('english', 'iphone') AS query
WHERE search_vector @@ query
ORDER BY rank DESC
LIMIT 20;
```

### Level 2: Search API Endpoint

```typescript
// Backend
router.get('/api/v1/products/search', async (req, res) => {
  const { q, category, minPrice, maxPrice, page = 1, limit = 20 } = req.query;

  let query = db('products').select('*');

  // Search
  if (q) {
    query = query.where((builder) => {
      builder.whereILike('name', `%${q}%`)
             .orWhereILike('description', `%${q}%`);
    });
  }

  // Filters
  if (category) query = query.where('category', category);
  if (minPrice) query = query.where('price', '>=', minPrice);
  if (maxPrice) query = query.where('price', '<=', maxPrice);

  // Pagination
  const offset = (page - 1) * limit;
  const [total] = await query.clone().count('* as count');
  const results = await query.limit(limit).offset(offset);

  res.json({
    success: true,
    data: results,
    meta: { page: +page, limit: +limit, total: +total.count },
  });
});
```

### Frontend Search with Debounce

```typescript
function SearchBar({ onSearch }: { onSearch: (query: string) => void }) {
  const [query, setQuery] = useState('');

  // Debounce search — chờ 300ms sau khi user ngừng typing
  useEffect(() => {
    const timer = setTimeout(() => {
      if (query.length >= 2) onSearch(query);
    }, 300);
    return () => clearTimeout(timer);
  }, [query, onSearch]);

  return (
    <input
      type="search"
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      placeholder="Search products..."
    />
  );
}
```

### Level 3: Elasticsearch (Advanced)

```typescript
// Khi nào cần Elasticsearch:
// - Dataset lớn (>100K records)
// - Full-text search phức tạp
// - Faceted search / aggregations
// - Fuzzy matching, synonyms
// - Search analytics

import { Client } from '@elastic/elasticsearch';

const elastic = new Client({ node: process.env.ELASTICSEARCH_URL });

// Index document
await elastic.index({
  index: 'products',
  id: product.id,
  document: {
    name: product.name,
    description: product.description,
    category: product.category,
    price: product.price,
  },
});

// Search
const result = await elastic.search({
  index: 'products',
  query: {
    multi_match: {
      query: 'iphone',
      fields: ['name^3', 'description'],  // name có weight x3
      fuzziness: 'AUTO',  // cho phép typo
    },
  },
});
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Search trên mỗi keystroke | Debounce 300ms |
| LIKE '%query%' trên bảng lớn | Full-text search hoặc Elasticsearch |
| Không sanitize search input | Sanitize + escape special characters |
| Elasticsearch cho dự án nhỏ | LIKE/ILIKE đủ cho < 10K records |
| Không cache search results | Cache popular searches |

---

## Liên kết

- **Skill liên quan:** [database-design](../database-design/SKILL.md), [performance-optimization](../performance-optimization/SKILL.md), [frontend-development](../frontend-development/SKILL.md)
