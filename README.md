# 🛠️ AI Agent Programming Toolkit

> Bộ toolkit toàn diện giúp AI agent triển khai dự án lập trình một cách **có hệ thống, nhanh gọn và không thiếu sót**.

## 🎯 Mục tiêu

Toolkit này được thiết kế để:
- **Hệ thống hóa** toàn bộ quy trình phát triển phần mềm
- **Giảm thiểu sai sót** bằng checklist và template chi tiết cho từng bước
- **Tăng tốc triển khai** nhờ hướng dẫn rõ ràng, ví dụ cụ thể
- **Phù hợp mọi AI agent** — không phụ thuộc vào nền tảng hay mô hình cụ thể

## 📦 Cấu trúc Toolkit

```
toolkit-for-ai-agent/
├── README.md                  # Hướng dẫn tổng quan (file này)
├── QUICK-START.md             # ⚡ Quick start + Project Templates (ĐỌC ĐẦU TIÊN)
├── WORKFLOW.md                # 🔄 Master workflow + Phase Gates + Master Checklist
├── processes/                 # 10 Process Kits (quy trình SDLC)
│   ├── 01-project-init/
│   ├── 02-requirements-analysis/
│   ├── 03-project-estimation/
│   ├── 04-system-design/
│   ├── 05-implementation/
│   ├── 06-testing/
│   ├── 07-code-review/
│   ├── 08-deployment/
│   ├── 09-documentation/
│   └── 10-maintenance/
└── skills/                    # 25 Skill Kits (kỹ năng kỹ thuật)
    ├── api-design/
    ├── database-design/
    ├── frontend-development/
    ├── backend-development/
    ├── authentication/
    ├── error-handling/
    ├── performance-optimization/
    ├── security/
    ├── ci-cd/
    ├── docker-containerization/
    ├── git-workflow/
    ├── logging-monitoring/
    ├── state-management/
    ├── real-time/
    ├── file-storage/
    ├── environment-config/
    ├── search-functionality/
    ├── payment-integration/
    ├── email-notification/
    ├── caching/
    ├── data-validation/
    ├── testing-tools/
    ├── internationalization/
    ├── queue-background-jobs/
    └── seo/
```

## 🔄 Cách sử dụng

### Bước 1: Đọc Quick Start
Bắt đầu bằng việc đọc [QUICK-START.md](./QUICK-START.md) để xác định nhanh cần đọc kits nào.

### Bước 2: Xác định phạm vi dự án
- **Dự án mới hoàn chỉnh**: Đi theo toàn bộ Process Kits từ 01 → 10
- **Một phần dự án**: Chỉ đọc Process Kit liên quan + Skill Kits cần thiết

### Bước 3: Áp dụng Process Kit
Mỗi Process Kit chứa file `SKILL.md` với:
- ✅ **Checklist** — các bước cần thực hiện
- 📖 **Hướng dẫn chi tiết** — giải thích từng bước
- 📝 **Templates** — code/config mẫu sẵn sàng sử dụng
- ⚠️ **Common Pitfalls** — lỗi thường gặp và cách tránh

### Bước 4: Áp dụng Skill Kit khi cần
Khi triển khai tính năng cụ thể, tham khảo Skill Kit tương ứng.

## 📋 Process Kits (Quy trình)

| # | Kit | Mô tả |
|---|-----|--------|
| 01 | [project-init](./processes/01-project-init/SKILL.md) | Khởi tạo dự án, chọn tech stack, setup cấu trúc |
| 02 | [requirements-analysis](./processes/02-requirements-analysis/SKILL.md) | Phân tích yêu cầu, user stories, acceptance criteria |
| 03 | [project-estimation](./processes/03-project-estimation/SKILL.md) | Ước lượng thời gian, resources, risk assessment |
| 04 | [system-design](./processes/04-system-design/SKILL.md) | Thiết kế kiến trúc, database, API, components |
| 05 | [implementation](./processes/05-implementation/SKILL.md) | Coding standards, patterns, cấu trúc module |
| 06 | [testing](./processes/06-testing/SKILL.md) | Unit test, integration test, e2e test |
| 07 | [code-review](./processes/07-code-review/SKILL.md) | Review checklist, best practices |
| 08 | [deployment](./processes/08-deployment/SKILL.md) | Build, deploy, rollback strategy |
| 09 | [documentation](./processes/09-documentation/SKILL.md) | README, API docs, changelog |
| 10 | [maintenance](./processes/10-maintenance/SKILL.md) | Bug tracking, updates, refactoring |

## 🧠 Skill Kits (Kỹ năng)

| Kit | Mô tả |
|-----|--------|
| [api-design](./skills/api-design/SKILL.md) | RESTful/GraphQL API design |
| [database-design](./skills/database-design/SKILL.md) | Schema, indexing, migrations |
| [frontend-development](./skills/frontend-development/SKILL.md) | Component architecture, responsive design |
| [backend-development](./skills/backend-development/SKILL.md) | Server architecture, middleware, routing |
| [authentication](./skills/authentication/SKILL.md) | JWT, OAuth, RBAC |
| [error-handling](./skills/error-handling/SKILL.md) | Error types, logging, user-friendly messages |
| [performance-optimization](./skills/performance-optimization/SKILL.md) | Caching, lazy loading, query optimization |
| [security](./skills/security/SKILL.md) | OWASP Top 10, input validation |
| [ci-cd](./skills/ci-cd/SKILL.md) | Pipeline design, automated deployment |
| [docker-containerization](./skills/docker-containerization/SKILL.md) | Dockerfile, docker-compose |
| [git-workflow](./skills/git-workflow/SKILL.md) | Branching, commit conventions |
| [logging-monitoring](./skills/logging-monitoring/SKILL.md) | Log levels, health checks, alerting |
| [state-management](./skills/state-management/SKILL.md) | Redux/Zustand/Pinia patterns |
| [real-time](./skills/real-time/SKILL.md) | WebSocket, SSE, pub/sub |
| [file-storage](./skills/file-storage/SKILL.md) | Upload, cloud storage, CDN |
| [environment-config](./skills/environment-config/SKILL.md) | .env, secrets, 12-factor app |
| [search-functionality](./skills/search-functionality/SKILL.md) | Full-text search, Elasticsearch |
| [payment-integration](./skills/payment-integration/SKILL.md) | Stripe, PayPal, VNPay |
| [email-notification](./skills/email-notification/SKILL.md) | Email, push notification, SMS |
| [caching](./skills/caching/SKILL.md) | Redis, Memcached, cache invalidation |
| [data-validation](./skills/data-validation/SKILL.md) | Zod, Pydantic, validation patterns |
| [testing-tools](./skills/testing-tools/SKILL.md) | Jest, Vitest, Pytest, Playwright |
| [internationalization](./skills/internationalization/SKILL.md) | i18n, multi-language, locale formatting |
| [queue-background-jobs](./skills/queue-background-jobs/SKILL.md) | BullMQ, Celery, job scheduling, retry |
| [seo](./skills/seo/SKILL.md) | Meta tags, structured data, Core Web Vitals |

## 📌 Lưu ý quan trọng

1. **Đọc [QUICK-START.md](./QUICK-START.md) đầu tiên** — Xác định loại dự án, kits cần đọc, và Project Templates
2. **Dùng [WORKFLOW.md](./WORKFLOW.md) để không bỏ sót** — Master Checklist + Phase Gates + quy trình tổng thể
3. **Thứ tự Process Kit là quan trọng** — Đi từ 01 → 10 để không bỏ sót bước
4. **Skill Kit dùng khi cần** — Tham khảo khi triển khai tính năng cụ thể
5. **Cross-references** — Theo dõi phần "Liên kết" ở cuối mỗi kit
