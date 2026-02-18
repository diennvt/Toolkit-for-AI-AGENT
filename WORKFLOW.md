# 🔄 Master Workflow — Quy trình triển khai dự án

> Workflow tổng thể hướng dẫn AI agent đi qua từng giai đoạn của dự án lập trình một cách có hệ thống.

## Quy trình tổng thể

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NHẬN YÊU CẦU TỪ USER                           │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 1: PLANNING (Lập kế hoạch)                                  │
│  ├── 01. Project Init        → Khởi tạo dự án, chọn tech stack     │
│  ├── 02. Requirements        → Phân tích yêu cầu, scope            │
│  └── 03. Estimation          → Ước lượng thời gian, resources       │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 2: DESIGN (Thiết kế)                                        │
│  └── 04. System Design       → Kiến trúc, DB, API, components      │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 3: BUILD (Xây dựng)                                         │
│  ├── 05. Implementation      → Code theo design + Skill Kits       │
│  ├── 06. Testing             → Viết test, chạy test                 │
│  └── 07. Code Review         → Review chất lượng code               │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 4: DELIVER (Bàn giao)                                       │
│  ├── 08. Deployment          → Build, deploy, verify                │
│  └── 09. Documentation       → Viết tài liệu                       │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 5: MAINTAIN (Bảo trì)                                       │
│  └── 10. Maintenance         → Bug fix, updates, refactoring        │
└─────────────────────────────────────────────────────────────────────┘
```

## Hướng dẫn áp dụng theo loại yêu cầu

### 🟢 Dự án mới hoàn chỉnh
Đi qua **tất cả 5 phase** theo thứ tự. Áp dụng Skill Kits tương ứng ở Phase 3.

```
Phase 1 → Phase 2 → Phase 3 → Phase 4 → Phase 5
```

### 🟡 Thêm tính năng mới
Bắt đầu từ **Phase 1** (bỏ qua Project Init nếu dự án đã có), tập trung vào Phase 2 & 3.

```
02-Requirements → 03-Estimation → 04-System Design → 05-Implementation → 06-Testing → 07-Code Review
```

### 🟠 Sửa bug / Fix issue
Đi thẳng vào **Phase 3**, tập trung vào Testing & Code Review.

```
05-Implementation (fix) → 06-Testing → 07-Code Review → 08-Deployment
```

### 🔴 Refactoring / Tối ưu
Bắt đầu từ **Code Review** để đánh giá, sau đó Implementation.

```
07-Code Review (đánh giá) → 04-System Design (nếu cần) → 05-Implementation → 06-Testing
```

## Khi nào sử dụng Skill Kits

Trong quá trình triển khai (**Phase 3 - Implementation**), tham khảo Skill Kits tương ứng:

| Đang làm gì? | Skill Kit cần tham khảo |
|---------------|------------------------|
| Thiết kế API | `api-design` |
| Tạo database | `database-design` |
| Code frontend | `frontend-development`, `state-management` |
| Code backend | `backend-development` |
| Tính năng login | `authentication` |
| Validate input | `data-validation` |
| Xử lý lỗi | `error-handling` |
| Tối ưu tốc độ | `performance-optimization`, `caching` |
| Bảo mật | `security` |
| Setup CI/CD | `ci-cd` |
| Docker hóa | `docker-containerization` |
| Quản lý Git | `git-workflow` |
| Logging | `logging-monitoring` |
| Tính năng real-time | `real-time` |
| Upload file | `file-storage` |
| Quản lý config | `environment-config` |
| Tính năng tìm kiếm | `search-functionality` |
| Tính năng thanh toán | `payment-integration` |
| Gửi email/thông báo | `email-notification` |
| Testing | `testing-tools` |
| Đa ngôn ngữ (i18n) | `internationalization` |
| Background jobs / Queue | `queue-background-jobs` |
| SEO | `seo` |

---

## 🚧 Phase Gates — Điều kiện bắt buộc trước khi chuyển phase

> **QUAN TRỌNG:** AI agent KHÔNG ĐƯỢC chuyển sang phase tiếp theo nếu chưa đạt đủ điều kiện.

### Gate 1: Planning → Design
| # | Điều kiện | Kiểm tra |
|---|-----------|----------|
| 1 | Tech stack đã chọn xong | Framework, DB, hosting xác định rõ |
| 2 | Requirements đầy đủ | User stories + acceptance criteria viết xong |
| 3 | Scope rõ ràng | Ranh giới "làm" và "KHÔNG làm" được ghi nhận |
| 4 | User đã xác nhận requirements | User đồng ý hoặc sign-off |

### Gate 2: Design → Build
| # | Điều kiện | Kiểm tra |
|---|-----------|----------|
| 1 | Database schema hoàn chỉnh | Entities, relationships, data types xác định |
| 2 | API endpoints list đầy đủ | Method + URL + request/response format |
| 3 | Auth flow xác định | Strategy (JWT/OAuth), token flow, roles |
| 4 | Frontend wireframe / page list | Pages + component hierarchy |

### Gate 3: Build → Deliver
| # | Điều kiện | Kiểm tra |
|---|-----------|----------|
| 1 | Tất cả tests pass | Unit + Integration tests xanh |
| 2 | Code review xong | Không có issues nghiêm trọng |
| 3 | Security review pass | No hardcoded secrets, proper auth, input validation |
| 4 | Linter/formatter pass | Không có warnings/errors |

### Gate 4: Deliver → Maintain
| # | Điều kiện | Kiểm tra |
|---|-----------|----------|
| 1 | Production hoạt động | Smoke test pass trên production |
| 2 | Docs đầy đủ | README, API docs, env vars docs |
| 3 | Monitoring setup | Error tracking + uptime monitoring |

---

# 📋 Master Checklist — Tất cả bước quan trọng

> **AI Agent: Dùng checklist này để đảm bảo KHÔNG BỎ SÓT bước nào.**
> Đánh dấu `[x]` cho từng bước khi hoàn thành. Nếu bước không áp dụng cho dự án, đánh `[N/A]`.

---

## PHASE 1: PLANNING (Lập kế hoạch)

### 01 — Project Initialization
- [ ] Xác định loại dự án (web app, API, mobile, CLI...)
- [ ] Chọn tech stack (language, framework, database)
- [ ] Khởi tạo repository + `.gitignore`
- [ ] Tạo cấu trúc thư mục chuẩn
- [ ] Cài dependencies + dev tools (linter, formatter)
- [ ] Tạo `.env.example` + setup environment
- [ ] Tạo `README.md` ban đầu
- [ ] Setup pre-commit hooks

### 02 — Requirements Analysis
- [ ] Thu thập yêu cầu từ user (ghi chép rõ ràng)
- [ ] Viết User Stories (As a ___, I want ___, so that ___)
- [ ] Xác định Acceptance Criteria cho mỗi story
- [ ] Phân loại MoSCoW (Must / Should / Could / Won't)
- [ ] ⚠️ **Xác định scope rõ ràng** — ranh giới "làm gì" và "KHÔNG làm gì"
- [ ] ⚠️ **Phòng chống scope creep** — ghi nhận mọi thay đổi scope
- [ ] Xác định Non-functional requirements (performance, security, scalability)
- [ ] User confirmation — user đồng ý requirements

### 03 — Project Estimation
- [ ] Breakdown tasks từ requirements
- [ ] Ước lượng complexity cho mỗi task (Simple / Medium / Complex)
- [ ] Xác định dependencies giữa các tasks
- [ ] Đánh giá rủi ro (Risk Assessment)
- [ ] Lập milestone plan
- [ ] Buffer time cho unexpected issues (+20-30%)

**🚧 PHASE GATE 1 → Trước khi chuyển sang Design:**
- [ ] ✅ Tech stack đã chọn xong
- [ ] ✅ Requirements đầy đủ, user đã xác nhận
- [ ] ✅ Scope rõ ràng, có ranh giới
- [ ] ✅ Task breakdown + estimation xong

---

## PHASE 2: DESIGN (Thiết kế)

### 04 — System Design
- [ ] Chọn architecture pattern (Monolith / Microservices / Serverless)
- [ ] Thiết kế database schema (entities, relationships, indexes)
- [ ] Thiết kế API endpoints (method, URL, request/response format)
- [ ] Thiết kế authentication flow (JWT, OAuth...)
- [ ] Thiết kế authorization rules (RBAC)
- [ ] Thiết kế frontend pages + component hierarchy
- [ ] Thiết kế error response format chuẩn
- [ ] Xác định external services cần tích hợp
- [ ] Review security considerations (OWASP)

**🚧 PHASE GATE 2 → Trước khi bắt đầu code:**
- [ ] ✅ Database schema hoàn chỉnh
- [ ] ✅ API endpoints list đầy đủ
- [ ] ✅ Auth flow rõ ràng
- [ ] ✅ Component hierarchy / wireframe xong
- [ ] ✅ Design document đã review

---

## PHASE 3: BUILD (Xây dựng)

### 05 — Implementation
- [ ] Setup database + run migrations
- [ ] Implement models / ORM setup
- [ ] Implement repositories (data access layer)
- [ ] Implement services (business logic)
- [ ] Implement controllers (request handlers)
- [ ] Implement API routes + middleware chain
- [ ] Implement input validation (Zod / Pydantic)
- [ ] Implement error handling (custom errors + global handler)
- [ ] Implement authentication (register, login, JWT)
- [ ] Implement authorization middleware (RBAC)
- [ ] Implement frontend layout (Header, Sidebar, Footer)
- [ ] Implement frontend pages + routing
- [ ] Implement forms + form validation
- [ ] Implement API client (Axios interceptors)
- [ ] Implement loading / error / empty states
- [ ] Responsive design (mobile-first)
- [ ] Accessibility cơ bản (semantic HTML, ARIA, keyboard nav)

### 06 — Testing
- [ ] Unit tests cho business logic (services)
- [ ] Integration tests cho API endpoints
- [ ] Validate input validation (edge cases)
- [ ] Test authentication flows
- [ ] Test authorization (role-based access)
- [ ] Test error handling (error responses đúng format)
- [ ] E2E tests cho critical user flows (nếu cần)
- [ ] Chạy test coverage (target ≥ 80%)

### 07 — Code Review
- [ ] **Correctness** — Logic đúng, handles edge cases
- [ ] **Security** — No SQL injection, XSS, hardcoded secrets
- [ ] **Performance** — No N+1 queries, proper indexing
- [ ] **Readability** — Naming rõ ràng, functions ngắn gọn
- [ ] **DRY** — Không duplicate code
- [ ] **Error handling** — Mọi error path được xử lý
- [ ] **Input validation** — Validate ở boundaries
- [ ] **Logging** — Đủ logs cho debugging production
- [ ] **No TODO/FIXME** — Resolve trước khi merge

**🚧 PHASE GATE 3 → Trước khi deploy:**
- [ ] ✅ Tất cả tests pass
- [ ] ✅ Code review xong, không có issues nghiêm trọng
- [ ] ✅ Security review pass (no hardcoded secrets, proper auth)
- [ ] ✅ Linter pass, no warnings

---

## PHASE 4: DELIVER (Bàn giao)

### 08 — Deployment
- [ ] Setup environment variables cho target environment
- [ ] Build production bundle (optimize, minify)
- [ ] Setup hosting / server
- [ ] Setup database cho production
- [ ] Configure HTTPS / SSL
- [ ] Configure CORS cho production domain
- [ ] Setup CI/CD pipeline (auto-test + deploy)
- [ ] Deploy to staging → test → deploy to production
- [ ] Verify production deployment (smoke test)
- [ ] Setup rollback plan

### 09 — Documentation
- [ ] README.md hoàn chỉnh (setup, run, deploy instructions)
- [ ] API documentation (endpoints, request/response examples)
- [ ] Environment variables documentation
- [ ] Database schema documentation
- [ ] CHANGELOG.md (nếu versioned)
- [ ] Inline code comments cho logic phức tạp

**🚧 PHASE GATE 4 → Trước khi bàn giao:**
- [ ] ✅ Production hoạt động ổn định
- [ ] ✅ Docs đầy đủ cho người tiếp nhận
- [ ] ✅ Smoke test pass trên production

---

## PHASE 5: MAINTAIN (Bảo trì)

### 10 — Maintenance
- [ ] Setup error tracking (Sentry, DataDog...)
- [ ] Setup uptime monitoring
- [ ] Plan backup strategy cho database
- [ ] Lập lịch dependency updates
- [ ] Xác định quy trình bug tracking
- [ ] Viết runbook cho common operations
- [ ] Plan performance monitoring

---

## ⚡ Checklist rút gọn — Bước tối thiểu KHÔNG ĐƯỢC BỎ

Nếu thời gian hạn chế, tối thiểu phải hoàn thành các bước sau:

```
□ Chọn tech stack + setup project
□ Viết requirements rõ ràng + xác định scope
□ Thiết kế database schema + API endpoints
□ Implement theo đúng layer (Model → Service → Controller → Route)
□ Input validation cho MỌI endpoint
□ Error handling (custom errors + global handler)
□ Authentication + Authorization
□ Viết unit tests cho critical business logic
□ Security review (no secrets, no injection, rate limiting)
□ README + API docs
□ Deploy + verify
```

---

## Nguyên tắc quan trọng

1. **Không bỏ qua bước** — Mỗi bước trong process đều có lý do tồn tại
2. **Phase Gates là BẮT BUỘC** — Kiểm tra điều kiện trước khi chuyển phase
3. **Checklist trước, code sau** — Luôn review checklist của process kit trước khi bắt tay vào code
4. **Skill Kit là tham khảo** — Không bắt buộc dùng tất cả, chỉ dùng cái cần
5. **Iteration > Perfection** — Hoàn thành trước, tối ưu sau
6. **Documentation đi kèm code** — Viết docs song song với code, không để sau
7. **Khi không chắc → hỏi user** — Đừng giả định requirements
