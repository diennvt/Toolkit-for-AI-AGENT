---
name: Documentation
description: Tài liệu hóa - README, API docs, inline comments, changelog
---

# 09 — Documentation (Tài liệu hóa)

## Tổng quan

Kit này hướng dẫn viết documentation cho dự án: từ README, API docs, inline comments đến changelog. Tài liệu tốt giúp người khác (và AI agent khác) hiểu và sử dụng code dễ dàng.

**Khi nào sử dụng:** Song song với implementation, hoàn thiện trước/sau deployment.

---

## Checklist

### 1. README.md
- [ ] Tên project + mô tả ngắn
- [ ] Tech stack
- [ ] Hướng dẫn cài đặt (Installation)
- [ ] Hướng dẫn chạy (Getting Started)
- [ ] Environment variables cần thiết
- [ ] Scripts available (dev, build, test...)
- [ ] Cấu trúc thư mục
- [ ] License

### 2. API Documentation
- [ ] Liệt kê tất cả endpoints
- [ ] Request format (headers, body, params)
- [ ] Response format (success + error)
- [ ] Authentication requirements
- [ ] Example requests/responses

### 3. Code Documentation
- [ ] Comments cho logic phức tạp (WHY, không phải WHAT)
- [ ] JSDoc/docstring cho public functions
- [ ] Type definitions rõ ràng (TypeScript/Python type hints)

### 4. Changelog
- [ ] Theo format Semantic Versioning
- [ ] Ghi rõ: Added, Changed, Fixed, Removed

---

## Hướng dẫn chi tiết

### Template README.md

```markdown
# Project Name

Brief description of what this project does.

## Tech Stack

- **Frontend:** React, TypeScript, Tailwind CSS
- **Backend:** Node.js, Express, TypeScript
- **Database:** PostgreSQL
- **Cache:** Redis

## Getting Started

### Prerequisites
- Node.js >= 18
- PostgreSQL >= 15
- Redis >= 7

### Installation

1. Clone the repository:
   git clone https://github.com/user/project.git
   cd project

2. Install dependencies:
   npm install

3. Setup environment:
   cp .env.example .env
   # Edit .env with your values

4. Run database migrations:
   npm run migrate

5. Start development server:
   npm run dev

### Available Scripts

| Script | Description |
|--------|-------------|
| `npm run dev` | Start development server |
| `npm run build` | Build for production |
| `npm run test` | Run tests |
| `npm run lint` | Run linter |
| `npm run migrate` | Run database migrations |

## Project Structure

(Mô tả cấu trúc thư mục chính)

## Environment Variables

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| `APP_PORT` | Server port | No | 3000 |
| `DB_HOST` | Database host | Yes | — |
| `DB_NAME` | Database name | Yes | — |

## License

MIT
```

### Template API Documentation

```markdown
# API Documentation

## Base URL
`https://api.example.com/v1`

## Authentication
All authenticated endpoints require Bearer token in header:
Authorization: Bearer <token>

---

## Auth Endpoints

### POST /auth/login
Login and receive access token.

**Request Body:**
{
  "email": "user@example.com",
  "password": "password123"
}

**Success Response (200):**
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJ...",
    "user": { "id": "1", "email": "user@example.com" }
  }
}

**Error Response (401):**
{
  "success": false,
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Email or password is incorrect"
  }
}
```

### Template CHANGELOG.md

```markdown
# Changelog

All notable changes to this project will be documented in this file.

## [1.1.0] - 2025-01-15

### Added
- Search functionality with full-text search
- Email notification for new orders

### Changed
- Improved pagination performance
- Updated user profile UI

### Fixed
- Fixed login redirect loop
- Fixed cart total calculation

## [1.0.0] - 2025-01-01

### Added
- Initial release
- User authentication (login/register)
- Product CRUD
- Shopping cart
- Order management
```

### Inline Comments Best Practices

```javascript
// ✅ GOOD — Giải thích WHY
// Use exponential backoff to avoid overwhelming the API
// when rate limited (429 status)
const delay = Math.pow(2, attempt) * 1000;

// ✅ GOOD — Giải thích business rule
// Tax is only applied to orders above $50
// as per company policy effective Jan 2025
if (orderTotal > 50) {
  tax = orderTotal * 0.1;
}

// ❌ BAD — Giải thích WHAT (code đã tự giải thích)
// Increment counter by 1
counter++;

// ❌ BAD — Outdated comment
// Returns user name (actually returns full user object now)
function getUser() { ... }
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Không viết README | README là bắt buộc cho mọi project |
| Docs outdated | Cập nhật docs cùng lúc với code |
| Comment WHAT thay vì WHY | Giải thích lý do, không phải cách làm |
| API docs thiếu error responses | Document cả success VÀ error responses |
| Không có CHANGELOG | Theo Semantic Versioning |

---

## Liên kết

- **Trước đó:** [08-Deployment](../08-deployment/SKILL.md)
- **Tiếp theo:** [10-Maintenance](../10-maintenance/SKILL.md)
- **Skill liên quan:** [testing-tools](../../skills/testing-tools/SKILL.md), [api-design](../../skills/api-design/SKILL.md)

