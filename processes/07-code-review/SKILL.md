---
name: Code Review
description: Review code - checklist review, common issues, best practices
---

# 07 — Code Review (Review code)

## Tổng quan

Kit này cung cấp checklist và quy trình review code để đảm bảo chất lượng trước khi merge. AI agent sử dụng kit này để tự review code hoặc đánh giá code hiện có.

**Khi nào sử dụng:** Sau khi hoàn thành implementation và testing.

---

## Checklist

### 1. Correctness (Đúng)
- [ ] Code thực hiện đúng yêu cầu
- [ ] Logic xử lý đúng trong mọi trường hợp
- [ ] Edge cases được xử lý
- [ ] Error handling đầy đủ

### 2. Readability (Dễ đọc)
- [ ] Naming rõ ràng, mô tả đúng mục đích
- [ ] Functions ngắn gọn (< 30 lines)
- [ ] Files có kích thước hợp lý (< 300 lines)
- [ ] Comments giải thích WHY, không phải WHAT
- [ ] Không có dead code / commented-out code

### 3. Maintainability (Dễ bảo trì)
- [ ] Code DRY — không duplicate
- [ ] Single Responsibility — mỗi function/class 1 việc
- [ ] Loose coupling — modules ít phụ thuộc nhau
- [ ] Dễ extend — thêm tính năng không cần sửa nhiều
- [ ] Consistent style — theo cùng conventions

### 4. Security
- [ ] Input validation ở mọi boundary
- [ ] Không expose sensitive data (passwords, tokens, keys)
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (sanitize output)
- [ ] Authentication/Authorization đúng

### 5. Performance
- [ ] Không có N+1 queries
- [ ] Database queries có index
- [ ] Không load dữ liệu không cần thiết
- [ ] Async operations xử lý đúng
- [ ] Memory leaks prevention

### 6. Testing
- [ ] Unit tests cover business logic
- [ ] Integration tests cover API endpoints
- [ ] Tests chạy pass
- [ ] Test coverage đạt target

---

## Hướng dẫn chi tiết

### Review theo layers

```
Layer 1: Syntax & Style
├── Linter/formatter đã chạy chưa?
├── Naming conventions nhất quán?
└── Code formatting đồng nhất?

Layer 2: Logic & Correctness
├── Logic xử lý đúng?
├── Edge cases?
└── Error handling?

Layer 3: Architecture & Design
├── Đúng design pattern?
├── Tách biệt concerns?
└── Có tech debt không?

Layer 4: Security
├── Input validation?
├── Auth/authz?
└── Sensitive data?

Layer 5: Performance
├── Query optimization?
├── Caching?
└── Memory management?
```

### Red Flags — Dấu hiệu code có vấn đề

```
🔴 Function > 50 lines                    → Cần chia nhỏ
🔴 File > 500 lines                       → Cần tách module
🔴 Deeply nested (> 3 levels)             → Cần early return / extract
🔴 God class / God function               → Cần tách responsibilities
🔴 Magic numbers / strings                → Cần dùng constants
🔴 try {} catch (e) {} (empty catch)      → Cần handle hoặc log error
🔴 console.log trong production code      → Cần dùng proper logging
🔴 Hardcoded URLs / credentials           → Cần dùng env vars
🔴 No error handling on API calls         → Cần try-catch + error state
🔴 Direct DOM manipulation in React       → Cần dùng refs / state
```

### Refactoring Techniques

| Problem | Technique | Before → After |
|---------|-----------|----------------|
| Deep nesting | Early return | `if (x) { if (y) { ... } }` → `if (!x) return; if (!y) return; ...` |
| Duplicate code | Extract function | Code lặp lại → `shared function` |
| Long function | Extract method | 1 function 50 lines → 3 functions 15 lines |
| Complex condition | Extract variable | `if (a && b && !c)` → `const isValid = a && b && !c` |
| Mixed concerns | Separate layers | Controller + logic → Controller + Service |

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Review quá nitpicky (style nit) | Tập trung vào logic, security, architecture |
| Bỏ qua security review | Luôn check OWASP Top 10 |
| Chỉ review happy path | Review cả error handling paths |
| Approve mà không hiểu code | Hiểu rõ trước khi approve |
| Để dead code "phòng khi cần" | Xóa dead code, Git lưu history rồi |

---

## Liên kết

- **Trước đó:** [06-Testing](../06-testing/SKILL.md)
- **Tiếp theo:** [08-Deployment](../08-deployment/SKILL.md)
- **Skill liên quan:** [security](../../skills/security/SKILL.md), [performance-optimization](../../skills/performance-optimization/SKILL.md)
