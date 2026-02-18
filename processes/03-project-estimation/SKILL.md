---
name: Project Estimation
description: Ước lượng dự án - độ phức tạp, thời gian, resources, risk assessment
---

# 03 — Project Estimation (Ước lượng dự án)

## Tổng quan

Kit này hướng dẫn ước lượng quy mô, độ phức tạp và resources cần thiết cho dự án. Giúp AI agent đưa ra đánh giá hợp lý về khối lượng công việc và phát hiện rủi ro sớm.

**Khi nào sử dụng:** Sau khi phân tích yêu cầu, trước khi thiết kế hệ thống.

---

## Checklist

### 1. Phân tích độ phức tạp
- [ ] Đánh giá complexity mỗi tính năng (S/M/L/XL)
- [ ] Xác định các tính năng có độ phức tạp kỹ thuật cao
- [ ] Đếm số lượng entities/models chính
- [ ] Đếm số lượng API endpoints cần tạo
- [ ] Đếm số lượng trang/screen UI

### 2. Task Breakdown
- [ ] Chia nhỏ mỗi tính năng thành các task cụ thể
- [ ] Xác định dependencies giữa các task
- [ ] Sắp xếp thứ tự triển khai hợp lý

### 3. Risk Assessment
- [ ] Xác định các rủi ro kỹ thuật
- [ ] Xác định các điểm chưa rõ ràng (unknowns)
- [ ] Lập kế hoạch giảm thiểu rủi ro

### 4. Milestone Planning
- [ ] Chia dự án thành milestones rõ ràng
- [ ] Mỗi milestone có deliverable cụ thể
- [ ] Xác định MVP (Minimum Viable Product)

---

## Hướng dẫn chi tiết

### Bảng đánh giá Complexity

| Size | Mô tả | Ví dụ | Ước lượng effort |
|------|--------|-------|------------------|
| **S** | Đơn giản, ít logic, template có sẵn | Static page, simple form, basic CRUD | 1-2 giờ |
| **M** | Logic trung bình, có validation | Search + filter, form nhiều step, upload file | 3-6 giờ |
| **L** | Logic phức tạp, nhiều edge cases | Payment integration, role-based access, real-time chat | 1-2 ngày |
| **XL** | Rất phức tạp, cần research | AI/ML features, complex algorithms, third-party integrations | 3-5 ngày |

### Template Task Breakdown

```markdown
# Task Breakdown

## Feature: [Tên tính năng]
Complexity: [S/M/L/XL]

### Backend Tasks
- [ ] Tạo model/schema
- [ ] Tạo migration
- [ ] Viết API endpoint(s)
- [ ] Viết business logic
- [ ] Viết validation
- [ ] Viết unit tests

### Frontend Tasks
- [ ] Tạo component(s)
- [ ] Kết nối API
- [ ] Xử lý state
- [ ] Xử lý loading/error states
- [ ] Responsive design

### DevOps Tasks
- [ ] Cấu hình environment
- [ ] Setup database
- [ ] CI/CD pipeline update
```

### Template Risk Assessment

```markdown
# Risk Assessment

| # | Risk | Impact | Probability | Mitigation |
|---|------|--------|-------------|------------|
| R1 | Third-party API thay đổi | High | Low | Abstract integration layer |
| R2 | Performance bottleneck DB | High | Medium | Indexing + caching strategy |
| R3 | Yêu cầu thay đổi scope | Medium | High | Xác nhận scope rõ ràng trước |
| R4 | Security vulnerability | High | Medium | Follow OWASP guidelines |
```

### Template Milestone Planning

```markdown
# Milestones

## Milestone 1: MVP (Foundation)
- [ ] Project setup + database
- [ ] Core models + migrations
- [ ] Authentication (login/register)
- [ ] Basic CRUD cho entity chính
**Deliverable:** App chạy được với tính năng cơ bản

## Milestone 2: Core Features
- [ ] Full CRUD + validation
- [ ] Search + filter
- [ ] File upload
- [ ] Email notifications
**Deliverable:** App với đầy đủ tính năng chính

## Milestone 3: Enhancement
- [ ] Performance optimization
- [ ] Advanced features
- [ ] UI/UX polish
**Deliverable:** App sẵn sàng production

## Milestone 4: Production
- [ ] Security hardening
- [ ] Documentation
- [ ] CI/CD + deployment
- [ ] Monitoring setup
**Deliverable:** App live trên production
```

### Xác định thứ tự triển khai

```
1. Foundation (Nền tảng)
   └── Project init, DB setup, Auth
        │
2. Core Models (Dữ liệu chính)
   └── Models, migrations, seed data
        │
3. Core API (API chính)
   └── CRUD endpoints, validation
        │
4. Core UI (Giao diện chính)
   └── Pages, forms, lists
        │
5. Features (Tính năng)
   └── Search, filter, upload, notifications
        │
6. Polish (Hoàn thiện)
   └── Performance, security, testing, docs
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Ước lượng quá lạc quan | Nhân ước lượng × 1.5 cho buffer |
| Bỏ qua thời gian testing | Tính testing = 30-40% thời gian dev |
| Không tính thời gian setup | Setup ban đầu thường mất 10-20% |
| Chưa xác định MVP | Luôn xác định MVP trước |
| Dependencies không rõ ràng | Vẽ dependency graph trước khi code |
| Bỏ qua risk assessment | Liệt kê risks và mitigation plan |

---

## Liên kết

- **Trước đó:** [02-Requirements Analysis](../02-requirements-analysis/SKILL.md)
- **Tiếp theo:** [04-System Design](../04-system-design/SKILL.md)
