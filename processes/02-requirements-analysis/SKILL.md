---
name: Requirements Analysis
description: Phân tích yêu cầu - thu thập, phân loại, xác định scope, tạo user stories
---

# 02 — Requirements Analysis (Phân tích yêu cầu)

## Tổng quan

Kit này hướng dẫn phân tích và tổ chức yêu cầu dự án: biến yêu cầu mơ hồ của user thành danh sách tính năng rõ ràng, phân loại ưu tiên, và tạo acceptance criteria cho từng tính năng.

**Khi nào sử dụng:** Khi nhận yêu cầu từ user — dù là dự án mới hay thêm tính năng.

---

## Checklist

### 1. Thu thập yêu cầu
- [ ] Đọc kỹ yêu cầu từ user
- [ ] Xác định mục tiêu chính của dự án/tính năng
- [ ] Liệt kê các câu hỏi cần làm rõ
- [ ] Xác định stakeholders (ai sử dụng, ai quản lý)

### 2. Phân loại yêu cầu
- [ ] Phân biệt Functional vs Non-functional requirements
- [ ] Xác định core features vs nice-to-have
- [ ] Phân loại theo module/domain

### 3. Tạo User Stories
- [ ] Viết user stories cho từng tính năng
- [ ] Xác định acceptance criteria cho mỗi story
- [ ] Ước lượng độ phức tạp (S/M/L/XL)

### 4. Xác định Scope & Constraints
- [ ] Xác định rõ scope (trong/ngoài phạm vi)
- [ ] Liệt kê ràng buộc kỹ thuật
- [ ] Liệt kê ràng buộc nghiệp vụ
- [ ] Xác định dependencies với hệ thống khác

### 5. Xác nhận với User
- [ ] Tóm tắt yêu cầu cho user review
- [ ] Làm rõ các điểm chưa chắc chắn
- [ ] User đồng ý scope và ưu tiên

---

## Hướng dẫn chi tiết

### Phân tích yêu cầu từ prompt của user

Khi nhận yêu cầu, hãy phân tích theo framework sau:

```
1. WHO   — Ai sẽ sử dụng?
2. WHAT  — Họ cần làm gì?
3. WHY   — Tại sao họ cần điều đó?
4. HOW   — Có yêu cầu kỹ thuật cụ thể không?
5. WHEN  — Có deadline/milestone nào không?
```

### Template User Story

```
Là [vai trò người dùng],
Tôi muốn [hành động/tính năng],
Để [lợi ích/giá trị nhận được].

Acceptance Criteria:
- GIVEN [điều kiện tiên quyết]
  WHEN [hành động]
  THEN [kết quả mong đợi]
```

**Ví dụ:**

```
Là một khách hàng,
Tôi muốn tìm kiếm sản phẩm theo tên,
Để nhanh chóng tìm được sản phẩm cần mua.

Acceptance Criteria:
- GIVEN tôi đang ở trang chủ
  WHEN tôi nhập "áo thun" vào ô tìm kiếm
  THEN hiển thị danh sách sản phẩm có tên chứa "áo thun"
  
- GIVEN không có kết quả phù hợp
  WHEN tôi nhập keyword tìm kiếm
  THEN hiển thị thông báo "Không tìm thấy sản phẩm"
```

### Phân loại yêu cầu

#### Functional Requirements (FR)
Những gì hệ thống **phải làm được**:
- Đăng ký, đăng nhập
- CRUD sản phẩm
- Tìm kiếm, lọc
- Thanh toán
- Gửi email thông báo

#### Non-functional Requirements (NFR)
**Cách** hệ thống hoạt động:
- **Performance:** Trang load < 3s
- **Scalability:** Hỗ trợ 1000 concurrent users
- **Security:** HTTPS, mã hóa password
- **Availability:** 99.9% uptime
- **Usability:** Responsive trên mobile

### Ma trận ưu tiên (MoSCoW)

| Ưu tiên | Mô tả | Ví dụ |
|----------|--------|-------|
| **Must** | Bắt buộc, không có thì dự án thất bại | Login, CRUD chính |
| **Should** | Quan trọng nhưng có workaround | Tìm kiếm nâng cao, filter |
| **Could** | Tốt nếu có, nhưng không ảnh hưởng | Dark mode, animations |
| **Won't** | Không làm trong lần này | AI recommendations |

### Template tổng hợp yêu cầu

```markdown
# Project Requirements

## Mục tiêu dự án
[Mô tả ngắn gọn mục tiêu chính]

## Đối tượng sử dụng
- Vai trò 1: [mô tả]
- Vai trò 2: [mô tả]

## Functional Requirements

### MUST HAVE
| # | Tính năng | User Story | Complexity |
|---|-----------|------------|------------|
| F1 | ... | ... | M |
| F2 | ... | ... | L |

### SHOULD HAVE
| # | Tính năng | User Story | Complexity |
|---|-----------|------------|------------|
| F3 | ... | ... | S |

### COULD HAVE
| # | Tính năng | User Story | Complexity |
|---|-----------|------------|------------|
| F4 | ... | ... | S |

## Non-functional Requirements
| # | Category | Requirement |
|---|----------|-------------|
| NF1 | Performance | Page load < 3s |
| NF2 | Security | HTTPS, password hashing |

## Out of Scope
- [Liệt kê những gì KHÔNG nằm trong scope]

## Assumptions
- [Các giả định]

## Open Questions
- [Câu hỏi cần làm rõ với user]
```

### ⚠️ Phòng chống Scope Creep

> **Scope creep** = yêu cầu phát sinh liên tục, dự án ngày càng to, không bao giờ xong.

#### Nguyên tắc phòng chống

1. **Ghi rõ "Out of Scope"** ngay từ đầu — liệt kê những gì KHÔNG làm
2. **Mọi yêu cầu mới → Change Request** — không tự ý thêm feature
3. **MoSCoW là deadline** — chỉ MUST items cho v1.0, phần còn lại = v2.0
4. **Hỏi user khi phân vân** — "Feature này có nằm trong scope không?"

#### Template Change Request

```markdown
## Change Request #[number]

**Ngày:** [date]
**Yêu cầu:** [mô tả feature/thay đổi mới]
**Lý do:** [tại sao cần thêm]
**Impact:**
- Thời gian: +[X] giờ/ngày
- Ảnh hưởng modules: [list modules]
- Risk: [Low/Medium/High]

**Quyết định:** ☐ Approve → Thêm vào scope  |  ☐ Defer → v2.0  |  ☐ Reject
```

#### AI Agent nên tự kiểm tra

```
Trước khi implement mỗi feature, hỏi:
1. Feature này có trong danh sách MUST HAVE không?
   → Có → Implement
   → Không → Feature này có trong SHOULD HAVE không?
     → Có → Implement nếu còn thời gian
     → Không → DỪNG LẠI, hỏi user trước khi làm
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Nhảy thẳng vào code mà không phân tích | Phân tích → thiết kế → rồi mới code |
| Giả sử hiểu yêu cầu mà không hỏi lại | Hỏi user khi có điểm mơ hồ |
| Cố gắng làm tất cả cùng lúc | Phân chia ưu tiên rõ ràng (MoSCoW) |
| Yêu cầu quá mơ hồ (VD: "làm đẹp") | Chuyển thành cụ thể: "Responsive, dark mode support" |
| Bỏ qua Non-functional requirements | Luôn xác định: performance, security, scalability |
| Không xác định rõ scope | Liệt kê rõ "Out of Scope" |
| Tự ý thêm feature ngoài scope | Mọi thay đổi → Change Request → user approve |
| Không có "Out of Scope" section | Luôn viết rõ những gì KHÔNG làm |

---

## Liên kết

- **Trước đó:** [01-Project Init](../01-project-init/SKILL.md)
- **Tiếp theo:** [03-Project Estimation](../03-project-estimation/SKILL.md)
- **Skill liên quan:** [api-design](../../skills/api-design/SKILL.md), [database-design](../../skills/database-design/SKILL.md)
