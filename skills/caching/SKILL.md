---
name: Caching
description: Caching - Redis, Memcached, HTTP caching, cache invalidation strategies
---

# Caching

## Tổng quan

Skill kit này hướng dẫn implement caching: Redis, HTTP caching, cache patterns, và invalidation strategies.

---

## Checklist

- [ ] Xác định data cần cache
- [ ] Chọn caching solution (Redis / in-memory / CDN)
- [ ] Implement cache layer
- [ ] Setup TTL (Time-to-Live) hợp lý
- [ ] Implement cache invalidation
- [ ] Handle cache miss gracefully
- [ ] Monitor cache hit rate

---

## Hướng dẫn chi tiết

### Khi nào cần Cache?

```
✅ NÊN CACHE:
- Data ít thay đổi (categories, settings, config)
- Database queries tốn thời gian
- API responses từ third-party
- Session data
- Computed results (reports, statistics)

❌ KHÔNG NÊN CACHE:
- Data thay đổi liên tục (real-time prices)
- User-specific sensitive data (passwords)
- Data cần consistency tuyệt đối
```

### Redis Setup & Usage

```typescript
import Redis from 'ioredis';

const redis = new Redis(process.env.REDIS_URL);

// Cache service
class CacheService {
  // GET — lấy từ cache
  async get<T>(key: string): Promise<T | null> {
    const data = await redis.get(key);
    return data ? JSON.parse(data) : null;
  }

  // SET — lưu vào cache với TTL
  async set(key: string, data: any, ttlSeconds = 300): Promise<void> {
    await redis.set(key, JSON.stringify(data), 'EX', ttlSeconds);
  }

  // DELETE — xóa cache
  async del(key: string): Promise<void> {
    await redis.del(key);
  }

  // DELETE by pattern
  async delPattern(pattern: string): Promise<void> {
    const keys = await redis.keys(pattern);
    if (keys.length > 0) await redis.del(...keys);
  }
}

const cache = new CacheService();
```

### Cache-Aside Pattern (Most Common)

```typescript
// Check cache → if miss → query DB → store in cache → return
async function getProduct(id: string): Promise<Product> {
  const cacheKey = `product:${id}`;

  // 1. Check cache
  const cached = await cache.get<Product>(cacheKey);
  if (cached) return cached; // Cache HIT

  // 2. Cache MISS → query database
  const product = await productRepo.findById(id);
  if (!product) throw new NotFoundError('Product');

  // 3. Store in cache (5 minutes TTL)
  await cache.set(cacheKey, product, 300);

  return product;
}

// Invalidate khi update
async function updateProduct(id: string, data: Partial<Product>) {
  const product = await productRepo.update(id, data);

  // Invalidate cache
  await cache.del(`product:${id}`);
  await cache.delPattern('products:list:*'); // Invalidate list caches too

  return product;
}
```

### HTTP Caching Headers

```typescript
// Static assets — cache lâu (1 year)
app.use('/static', express.static('public', {
  maxAge: '1y',
  immutable: true,
}));

// API responses — cache ngắn
app.get('/api/categories', (req, res) => {
  res.set('Cache-Control', 'public, max-age=3600'); // 1 hour
  res.json({ data: categories });
});

// User-specific — no cache
app.get('/api/profile', authenticate, (req, res) => {
  res.set('Cache-Control', 'private, no-cache');
  res.json({ data: user });
});
```

### Cache Invalidation Strategies

| Strategy | Mô tả | Khi nào dùng |
|----------|--------|-------------|
| **TTL** | Tự hết hạn sau N giây | Data ít quan trọng |
| **Write-through** | Update cache khi write DB | Consistency quan trọng |
| **Write-behind** | Update cache, async write DB | Performance quan trọng |
| **Cache-aside** | App quản lý cache manually | Most common, flexible |
| **Event-based** | Invalidate khi có event | Microservices |

### TTL Guidelines

| Data Type | TTL | Lý do |
|-----------|-----|-------|
| Static config | 24h | Ít thay đổi |
| Product list | 5-15 min | Thay đổi trung bình |
| User session | 30 min | Security |
| Search results | 2-5 min | Fresh enough |
| Dashboard stats | 1-5 min | Near real-time |
| Rate limit counters | 15 min | Window-based |

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Cache everything | Chỉ cache hot data |
| Không invalidate khi update | Invalidate/update cache khi data thay đổi |
| TTL quá dài/quá ngắn | Phù hợp với frequency of change |
| Cache keys không consistent | Pattern rõ ràng: `resource:id` |
| Không handle cache failure | App vẫn chạy khi cache down (fallback to DB) |
| Cache user-specific data shared | Private cache cho user-specific data |

---

## Liên kết

- **Skill liên quan:** [performance-optimization](../performance-optimization/SKILL.md), [backend-development](../backend-development/SKILL.md)
