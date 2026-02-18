---
name: API Design
description: Thiết kế RESTful/GraphQL API - endpoints, status codes, versioning, pagination
---

# API Design (Thiết kế API)

## Tổng quan

Skill kit này hướng dẫn thiết kế API chuẩn: RESTful conventions, status codes, versioning, pagination, filtering, và error handling.

---

## Checklist

- [ ] Xác định API style (REST / GraphQL)
- [ ] Thiết kế URL structure theo RESTful conventions
- [ ] Xác định HTTP methods cho mỗi endpoint
- [ ] Thiết kế request/response format thống nhất
- [ ] Xác định status codes chuẩn
- [ ] Thiết kế pagination, sorting, filtering
- [ ] Thiết kế error response format
- [ ] Xác định versioning strategy
- [ ] Xác định rate limiting
- [ ] Viết API documentation

---

## Hướng dẫn chi tiết

### RESTful URL Conventions

```
✅ Đúng:
GET    /api/v1/users          # Danh sách users
POST   /api/v1/users          # Tạo user mới
GET    /api/v1/users/:id      # Chi tiết 1 user
PUT    /api/v1/users/:id      # Cập nhật toàn bộ user
PATCH  /api/v1/users/:id      # Cập nhật 1 phần user
DELETE /api/v1/users/:id      # Xóa user

# Nested resources
GET    /api/v1/users/:id/orders       # Orders của user
POST   /api/v1/users/:id/orders       # Tạo order cho user

# Actions (khi không phải CRUD)
POST   /api/v1/users/:id/activate     # Action đặc biệt
POST   /api/v1/auth/login             # Authentication

❌ Sai:
GET    /api/v1/getUsers               # Không dùng verb trong URL
POST   /api/v1/user                   # Dùng số nhiều
GET    /api/v1/Users                   # Không viết hoa
DELETE /api/v1/deleteUser/:id          # Verb thừa
```

### HTTP Status Codes

| Code | Meaning | Khi nào dùng |
|------|---------|-------------|
| **200** | OK | GET/PUT/PATCH thành công |
| **201** | Created | POST tạo resource thành công |
| **204** | No Content | DELETE thành công |
| **400** | Bad Request | Validation error, sai format |
| **401** | Unauthorized | Chưa đăng nhập / token hết hạn |
| **403** | Forbidden | Đã đăng nhập nhưng không có quyền |
| **404** | Not Found | Resource không tồn tại |
| **409** | Conflict | Duplicate (email đã tồn tại) |
| **422** | Unprocessable | Validation logic error |
| **429** | Too Many Requests | Rate limit exceeded |
| **500** | Internal Server Error | Lỗi server |

### Response Format chuẩn

```json
// ✅ Success - Single item
{
  "success": true,
  "data": {
    "id": "uuid-123",
    "name": "John Doe",
    "email": "john@example.com",
    "createdAt": "2025-01-15T10:30:00Z"
  }
}

// ✅ Success - List with pagination
{
  "success": true,
  "data": [
    { "id": "1", "name": "Item 1" },
    { "id": "2", "name": "Item 2" }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8
  }
}

// ✅ Error
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      { "field": "email", "message": "Email is required" },
      { "field": "password", "message": "Password must be at least 8 characters" }
    ]
  }
}
```

### Pagination, Sorting & Filtering

```
# Pagination
GET /api/v1/products?page=1&limit=20

# Sorting
GET /api/v1/products?sort=price&order=asc
GET /api/v1/products?sort=-createdAt          # Prefix - = descending

# Filtering
GET /api/v1/products?category=electronics&minPrice=100&maxPrice=500

# Search
GET /api/v1/products?search=iphone

# Combined
GET /api/v1/products?page=1&limit=20&sort=-price&category=electronics&search=iphone
```

### API Versioning

```
# URL versioning (recommended)
/api/v1/users
/api/v2/users

# Header versioning
Accept: application/vnd.api.v1+json
```

### Rate Limiting Headers

```
X-RateLimit-Limit: 100          # Max requests per window
X-RateLimit-Remaining: 95       # Remaining requests
X-RateLimit-Reset: 1623456789   # Window reset timestamp
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Verb trong URL (`/getUsers`) | Dùng noun + HTTP method (`GET /users`) |
| Inconsistent response format | Cùng 1 format cho tất cả endpoints |
| Trả 200 cho mọi response | Dùng đúng status codes |
| Không có pagination | Luôn paginate list endpoints |
| Không versioning | Version từ đầu (`/api/v1/`) |
| Error response khác nhau | Chuẩn hóa error format |

---

## Liên kết

- **Process liên quan:** [04-System Design](../../processes/04-system-design/SKILL.md), [05-Implementation](../../processes/05-implementation/SKILL.md)
- **Skill liên quan:** [backend-development](../backend-development/SKILL.md), [authentication](../authentication/SKILL.md), [error-handling](../error-handling/SKILL.md)
