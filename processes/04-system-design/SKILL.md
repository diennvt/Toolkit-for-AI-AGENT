---
name: System Design
description: Thiết kế hệ thống - kiến trúc, database schema, API endpoints, component diagram
---

# 04 — System Design (Thiết kế hệ thống)

## Tổng quan

Kit này hướng dẫn thiết kế hệ thống phần mềm: từ kiến trúc tổng thể, database schema, API design, đến component structure. Là bước quan trọng nhất trước khi code.

**Khi nào sử dụng:** Sau khi có yêu cầu rõ ràng và ước lượng xong.

---

## Checklist

### 1. Kiến trúc tổng thể
- [ ] Chọn architecture pattern (Monolith, Microservices, Serverless...)
- [ ] Vẽ high-level system diagram
- [ ] Xác định các components chính
- [ ] Xác định cách giao tiếp giữa các components
- [ ] Xác định external services cần tích hợp

### 2. Database Design
- [ ] Liệt kê tất cả entities
- [ ] Thiết kế relationships (1:1, 1:N, N:N)
- [ ] Viết database schema chi tiết
- [ ] Xác định indexes cần thiết
- [ ] Thiết kế migration strategy

### 3. API Design
- [ ] Liệt kê tất cả API endpoints
- [ ] Xác định HTTP methods cho mỗi endpoint
- [ ] Thiết kế request/response format
- [ ] Xác định authentication cho mỗi endpoint
- [ ] Thiết kế error response format

### 4. Frontend Architecture
- [ ] Liệt kê tất cả pages/screens
- [ ] Thiết kế component hierarchy
- [ ] Xác định state management strategy
- [ ] Thiết kế routing structure
- [ ] Xác định shared components

### 5. Security Design
- [ ] Thiết kế authentication flow
- [ ] Xác định authorization rules (RBAC)
- [ ] Xác định data encryption cần thiết
- [ ] Review OWASP Top 10

---

## Hướng dẫn chi tiết

### Chọn Architecture Pattern

| Pattern | Khi nào dùng | Ưu điểm | Nhược điểm |
|---------|-------------|----------|-------------|
| **Monolith** | Dự án nhỏ-trung bình, team nhỏ | Đơn giản, dễ deploy | Khó scale, tightly coupled |
| **Modular Monolith** | Dự án trung bình, cần tổ chức tốt | Tốt nhất của cả hai | Cần kỷ luật team |
| **Microservices** | Dự án lớn, nhiều team | Scale độc lập | Complex, overhead lớn |
| **Serverless** | Event-driven, traffic không đều | Auto-scale, pay-per-use | Cold start, vendor lock-in |

### Template Database Schema

```sql
-- ==========================================
-- Entity: Users
-- ==========================================
CREATE TABLE users (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email       VARCHAR(255) UNIQUE NOT NULL,
    password    VARCHAR(255) NOT NULL,
    full_name   VARCHAR(255) NOT NULL,
    role        VARCHAR(50) DEFAULT 'user',
    is_active   BOOLEAN DEFAULT true,
    created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- ==========================================
-- Entity: [Entity Name]
-- Description: [Mô tả ngắn]
-- ==========================================
CREATE TABLE [table_name] (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    -- Fields
    [field_name]  [TYPE] [CONSTRAINTS],
    -- Relationships
    user_id     UUID REFERENCES users(id) ON DELETE CASCADE,
    -- Timestamps
    created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_[table]_[field] ON [table_name]([field_name]);
```

### Template API Endpoints

```markdown
# API Endpoints

## Base URL: `/api/v1`

### Auth
| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/auth/register` | Đăng ký | No |
| POST | `/auth/login` | Đăng nhập | No |
| POST | `/auth/refresh` | Refresh token | Yes |
| POST | `/auth/logout` | Đăng xuất | Yes |

### Users
| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/users/me` | Profile hiện tại | Yes |
| PUT | `/users/me` | Cập nhật profile | Yes |
| GET | `/users` | Danh sách users | Admin |
| GET | `/users/:id` | Chi tiết user | Admin |

### [Resource]
| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/[resources]` | Danh sách | Depends |
| POST | `/[resources]` | Tạo mới | Yes |
| GET | `/[resources]/:id` | Chi tiết | Depends |
| PUT | `/[resources]/:id` | Cập nhật | Yes |
| DELETE | `/[resources]/:id` | Xóa | Yes |
```

### Template Response Format

```json
// Success Response
{
  "success": true,
  "data": { ... },
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "totalPages": 5
  }
}

// Error Response
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email is required",
    "details": [
      {
        "field": "email",
        "message": "Email is required"
      }
    ]
  }
}
```

### Frontend Page Structure

```markdown
# Pages & Routes

| Route | Page | Components | Auth |
|-------|------|------------|------|
| `/` | Home | Hero, FeatureList | No |
| `/login` | Login | LoginForm | No |
| `/register` | Register | RegisterForm | No |
| `/dashboard` | Dashboard | Stats, RecentActivity | Yes |
| `/[resource]` | List | SearchBar, DataTable, Pagination | Yes |
| `/[resource]/new` | Create | ResourceForm | Yes |
| `/[resource]/:id` | Detail | ResourceDetail, Actions | Yes |
| `/[resource]/:id/edit` | Edit | ResourceForm (prefilled) | Yes |
```

### Component Hierarchy Template

```
App
├── Layout
│   ├── Header (Logo, Navigation, UserMenu)
│   ├── Sidebar (MenuItems)
│   └── Footer
├── Pages
│   ├── HomePage
│   ├── AuthPages (Login, Register)
│   └── ResourcePages (List, Detail, Create, Edit)
└── Shared Components
    ├── UI (Button, Input, Modal, Toast, Loading)
    ├── Forms (FormField, Select, DatePicker, FileUpload)
    └── Data (DataTable, Pagination, SearchBar, FilterPanel)
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Thiết kế quá phức tạp cho dự án nhỏ | Bắt đầu với monolith, refactor sau |
| Không nghĩ về scalability | Thiết kế để dễ scale khi cần |
| Database schema thiếu indexes | Xác định indexes cho queries thường dùng |
| API không consistent | Dùng RESTful conventions nhất quán |
| Bỏ qua error handling trong design | Thiết kế error response chuẩn từ đầu |
| Frontend monolith component | Chia nhỏ components, max 200 LOC/component |

---

## Liên kết

- **Trước đó:** [03-Project Estimation](../03-project-estimation/SKILL.md)
- **Tiếp theo:** [05-Implementation](../05-implementation/SKILL.md)
- **Skill liên quan:** [api-design](../../skills/api-design/SKILL.md), [database-design](../../skills/database-design/SKILL.md), [frontend-development](../../skills/frontend-development/SKILL.md), [backend-development](../../skills/backend-development/SKILL.md)
