# ⚡ Quick Start — Hướng dẫn nhanh cho AI Agent

> Đọc file này **đầu tiên** để xác định cần làm gì, đọc kits nào, bỏ qua kits nào.

### 📌 Tài liệu hỗ trợ
- **[WORKFLOW.md](./WORKFLOW.md)** — Quy trình tổng thể + Phase Gates + Master Checklist

---

## Bước 1: Xác định loại yêu cầu

```
User muốn gì?
│
├── 🟢 Dự án mới từ đầu ──────────────── → Đi đến [Luồng A]
├── 🟡 Thêm feature mới ──────────────── → Đi đến [Luồng B]
├── 🟠 Sửa bug / Fix issue ───────────── → Đi đến [Luồng C]
├── 🔴 Refactoring / Tối ưu ──────────── → Đi đến [Luồng D]
└── 🔵 Chỉ cần tư vấn / trả lời ─────── → Đi đến [Luồng E]
```

---

## Bước 2: Đi theo luồng phù hợp

### 🟢 Luồng A — Dự án mới hoàn chỉnh

> 💡 **Tip:** Biết rõ loại dự án? Xem [Project Templates](#-project-templates--gói-preset-theo-loại-dự-án) bên dưới để lấy danh sách skill kits gọn nhất.

```
Đọc THEO THỨ TỰ:
1. processes/01-project-init        ← Chọn tech stack, setup project
2. processes/02-requirements        ← Phân tích yêu cầu user
3. processes/03-project-estimation  ← Ước lượng scope
   ── 🚧 Phase Gate 1: Kiểm tra WORKFLOW.md trước khi sang Design ──
4. processes/04-system-design       ← Thiết kế DB, API, components
   ── 🚧 Phase Gate 2: Kiểm tra WORKFLOW.md trước khi sang Build ──
5. processes/05-implementation      ← Bắt đầu code
   └── Tham khảo Skill Kits cần thiết (xem Bước 3)
6. processes/06-testing             ← Viết tests
7. processes/07-code-review         ← Self-review
   ── 🚧 Phase Gate 3: Kiểm tra WORKFLOW.md trước khi sang Deliver ──
8. processes/08-deployment          ← Deploy
9. processes/09-documentation       ← Viết docs
10. processes/10-maintenance        ← Bàn giao, maintenance plan
```

### 🟡 Luồng B — Thêm feature mới

```
Đọc:
1. processes/02-requirements        ← Hiểu rõ feature cần thêm
2. processes/04-system-design       ← Design cho feature mới
3. processes/05-implementation      ← Code
   └── Tham khảo Skill Kits cần thiết (xem Bước 3)
4. processes/06-testing             ← Test feature mới
5. processes/07-code-review         ← Review
```

### 🟠 Luồng C — Sửa bug

```
Đọc:
1. processes/06-testing             ← Viết test tái hiện bug
2. processes/05-implementation      ← Fix bug
3. processes/07-code-review         ← Review fix
   └── Skill Kits: error-handling, logging-monitoring
```

### 🔴 Luồng D — Refactoring / Tối ưu

```
Đọc:
1. processes/07-code-review         ← Đánh giá code hiện tại
2. processes/04-system-design       ← Redesign nếu cần
3. processes/05-implementation      ← Refactor
4. processes/06-testing             ← Đảm bảo không regression
   └── Skill Kits: performance-optimization, caching, security
```

### 🔵 Luồng E — Tư vấn / Trả lời câu hỏi

```
Tìm Skill Kit phù hợp nhất trong Bước 3 → Trả lời dựa trên kit đó.
Không cần đọc Process Kits.
```

---

## Bước 3: Chọn Skill Kits theo tính năng

```
Tính năng cần implement?
│
├── API / Backend
│   ├── Thiết kế API          → skills/api-design
│   ├── Code backend          → skills/backend-development
│   ├── Database              → skills/database-design
│   ├── Xác thực / Login      → skills/authentication
│   ├── Validate input        → skills/data-validation
│   └── Xử lý lỗi            → skills/error-handling
│
├── Frontend
│   ├── Code frontend         → skills/frontend-development
│   ├── Quản lý state         → skills/state-management
│   └── Đa ngôn ngữ (i18n)   → skills/internationalization
│
├── Tính năng cụ thể
│   ├── Real-time (chat, live) → skills/real-time
│   ├── Upload file            → skills/file-storage
│   ├── Tìm kiếm              → skills/search-functionality
│   ├── Thanh toán             → skills/payment-integration
│   ├── Email / Thông báo     → skills/email-notification
│   ├── Cache                 → skills/caching
│   └── Background jobs       → skills/queue-background-jobs
│
├── DevOps / Infra
│   ├── CI/CD pipeline        → skills/ci-cd
│   ├── Docker                → skills/docker-containerization
│   ├── Environment config    → skills/environment-config
│   ├── Git workflow          → skills/git-workflow
│   └── Logging & monitoring  → skills/logging-monitoring
│
├── Chất lượng code
│   ├── Testing               → skills/testing-tools
│   ├── Tối ưu performance    → skills/performance-optimization
│   ├── Bảo mật              → skills/security
│   └── SEO                  → skills/seo
│
└── Không tìm thấy?
    → Dùng best practices từ processes/05-implementation
```

---

## Bước 4: Chọn template theo Tech Stack

```
Tech Stack?
│
├── Node.js / TypeScript ────── Đọc code examples mặc định trong mỗi kit
├── Python / FastAPI ────────── Tìm phần "Python / FastAPI" trong kit
├── Python / Django ─────────── Tìm phần "Python / Django" trong kit
└── Khác ────────────────────── Adapt patterns từ examples có sẵn
```

---

## Nguyên tắc vàng cho AI Agent

1. **Checklist trước, code sau** — Đọc checklist của kit trước khi code
2. **Đừng skip steps** — Mỗi bước tồn tại có lý do
3. **Phase Gate trước khi chuyển phase** — Kiểm tra [WORKFLOW.md](./WORKFLOW.md) sau mỗi phase
4. **Tham khảo Common Pitfalls** — Tránh sai lầm phổ biến
5. **Cross-references** — Follow links "Liên kết" ở cuối mỗi kit
6. **Iteration > Perfection** — Hoàn thành MVP trước, optimize sau
7. **Hỏi user khi không chắc** — Đừng giả định requirements

---

# 🎯 Project Templates — Gói preset theo loại dự án

> Chọn template gần nhất với dự án của user → đọc đúng skill kits cần thiết, bỏ qua cái không liên quan.

### Cách sử dụng
1. Xác định loại dự án từ yêu cầu user
2. Tìm template phù hợp nhất bên dưới
3. Đọc các Process Kits theo thứ tự
4. Chỉ đọc Skill Kits được liệt kê, bỏ qua phần còn lại

---

## 📦 Template A — REST API (Backend only)

**Khi nào:** User muốn xây dựng API server, không có frontend.

### Process Kits (theo thứ tự)
```
01-project-init → 02-requirements → 03-estimation → 04-system-design → 05-implementation → 06-testing → 07-code-review → 08-deployment → 09-documentation
```

### Skill Kits cần đọc
| Bắt buộc | Tùy dự án |
|----------|-----------|
| `api-design` | `search-functionality` |
| `backend-development` | `payment-integration` |
| `database-design` | `email-notification` |
| `authentication` | `real-time` |
| `data-validation` | `file-storage` |
| `error-handling` | `caching` |
| `environment-config` | `docker-containerization` |
| `testing-tools` | `ci-cd` |
| `security` | `queue-background-jobs` |

### Không cần
`frontend-development`, `state-management`, `internationalization`

---

## 🌐 Template B — Full-stack Web App

**Khi nào:** User muốn web app hoàn chỉnh (cả frontend + backend).

### Process Kits (theo thứ tự)
```
01 → 02 → 03 → 04 → 05 → 06 → 07 → 08 → 09 → 10
(Đi qua TẤT CẢ process kits)
```

### Skill Kits cần đọc
| Bắt buộc | Tùy dự án |
|----------|-----------|
| `api-design` | `search-functionality` |
| `backend-development` | `payment-integration` |
| `frontend-development` | `email-notification` |
| `database-design` | `real-time` |
| `authentication` | `file-storage` |
| `state-management` | `caching` |
| `data-validation` | `internationalization` |
| `error-handling` | `docker-containerization` |
| `environment-config` | `queue-background-jobs` |
| `testing-tools` | `seo` |
| `security` | |
| `git-workflow` | |

---

## 🛒 Template C — E-commerce

**Khi nào:** Dự án bán hàng online, marketplace, shopping platform.

### Process Kits
```
01 → 02 → 03 → 04 → 05 → 06 → 07 → 08 → 09 → 10
(Đi qua TẤT CẢ)
```

### Skill Kits cần đọc
| Bắt buộc | Khuyến nghị mạnh |
|----------|-----------------|
| `api-design` | `caching` |
| `backend-development` | `docker-containerization` |
| `frontend-development` | `ci-cd` |
| `database-design` | `logging-monitoring` |
| `authentication` | `internationalization` |
| `state-management` | |
| `data-validation` | |
| `error-handling` | |
| **`payment-integration`** ⭐ | |
| **`search-functionality`** ⭐ | |
| `email-notification` | |
| `file-storage` (product images) | |
| `security` | |
| `testing-tools` | |
| `environment-config` | |
| **`queue-background-jobs`** ⭐ | |
| **`seo`** ⭐ | |

---

## 🚀 Template D — SaaS MVP

**Khi nào:** Minimum Viable Product cho startup, cần ship nhanh.

### Process Kits (rút gọn)
```
01-project-init → 02-requirements → 04-system-design → 05-implementation → 06-testing → 08-deployment → 09-documentation
```
> **Lưu ý:** Bỏ qua `03-estimation` và `10-maintenance` cho MVP. Quay lại sau khi validate.

### Skill Kits cần đọc (chỉ cái cần thiết)
| Bắt buộc | Phase 2 (sau MVP) |
|----------|------------------|
| `api-design` | `performance-optimization` |
| `backend-development` | `caching` |
| `frontend-development` | `docker-containerization` |
| `database-design` | `ci-cd` |
| `authentication` | `logging-monitoring` |
| `data-validation` | `search-functionality` |
| `error-handling` | |
| `environment-config` | |

### Nguyên tắc MVP
1. Feature cốt lõi TRƯỚC → polish SAU
2. Đủ tests cho critical paths (auth, payment...)
3. Deploy sớm, iterate nhanh
4. Không optimize sớm — ship trước, optimize khi có data

---

## 💬 Template E — Real-time App (Chat, Live Dashboard)

**Khi nào:** Chat app, live notification, collaborative editing, live dashboard.

### Process Kits
```
01 → 02 → 03 → 04 → 05 → 06 → 07 → 08 → 09
```

### Skill Kits cần đọc
| Bắt buộc | Tùy dự án |
|----------|-----------|
| `api-design` | `file-storage` |
| `backend-development` | `search-functionality` |
| `frontend-development` | `docker-containerization` |
| `database-design` | `ci-cd` |
| `authentication` | |
| `state-management` | |
| **`real-time`** ⭐ | |
| `data-validation` | |
| `error-handling` | |
| `caching` (message cache) | |
| `email-notification` | |
| `testing-tools` | |

---

## 📱 Template F — Static Site / Landing Page

**Khi nào:** Marketing site, portfolio, blog, landing page — không cần backend.

### Process Kits (rút gọn)
```
01-project-init → 02-requirements → 05-implementation → 07-code-review → 08-deployment
```

### Skill Kits cần đọc
| Bắt buộc | Tùy dự án |
|----------|-----------|
| `frontend-development` | `internationalization` |
| `git-workflow` | `ci-cd` |
| `environment-config` | `seo` |

### Không cần
`backend-development`, `database-design`, `authentication`, `api-design`, `payment-integration`, `state-management` và hầu hết skill kits backend.

---

## 🔌 Template G — Microservices / API Gateway

**Khi nào:** Hệ thống lớn, nhiều services giao tiếp.

### Process Kits
```
01 → 02 → 03 → 04 → 05 → 06 → 07 → 08 → 09 → 10
(Tất cả — hệ thống lớn không nên skip)
```

### Skill Kits cần đọc
| Bắt buộc | Khuyến nghị mạnh |
|----------|-----------------|
| `api-design` | `real-time` |
| `backend-development` | `search-functionality` |
| `database-design` | `payment-integration` |
| `authentication` | `email-notification` |
| `data-validation` | `file-storage` |
| `error-handling` | `internationalization` |
| **`docker-containerization`** ⭐ | |
| **`ci-cd`** ⭐ | |
| **`logging-monitoring`** ⭐ | |
| **`caching`** ⭐ | |
| `environment-config` | |
| `security` | |
| `testing-tools` | |
| `git-workflow` | |
| `performance-optimization` | |

---

## Không tìm thấy template phù hợp?

1. Bắt đầu từ **Template B (Full-stack)** làm baseline
2. Loại bỏ skill kits KHÔNG liên quan
3. Thêm skill kits cụ thể cho domain
4. Khi phân vân → đọc kit đó, bỏ qua nếu thấy không cần
