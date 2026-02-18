---
name: Maintenance
description: Bảo trì & nâng cấp - bug tracking, version updates, dependency management, refactoring
---

# 10 — Maintenance (Bảo trì & Nâng cấp)

## Tổng quan

Kit này hướng dẫn quy trình bảo trì hệ thống sau khi deploy: xử lý bugs, cập nhật dependencies, refactoring, và cải tiến liên tục.

**Khi nào sử dụng:** Sau khi dự án đã live, hoặc khi cần sửa bug / nâng cấp.

---

## Checklist

### 1. Bug Tracking & Fixing
- [ ] Thu thập bug report (mô tả, steps to reproduce, expected vs actual)
- [ ] Phân loại severity (Critical / High / Medium / Low)
- [ ] Xác định root cause
- [ ] Viết fix + unit test cho bug
- [ ] Verify fix không gây side effects
- [ ] Deploy fix

### 2. Dependency Management
- [ ] Check outdated dependencies (`npm outdated`, `pip list --outdated`)
- [ ] Review changelogs của dependencies trước khi update
- [ ] Update minor/patch versions trước
- [ ] Test sau khi update
- [ ] Check security vulnerabilities (`npm audit`, `safety check`)

### 3. Performance Monitoring
- [ ] Monitor response times
- [ ] Monitor error rates
- [ ] Monitor resource usage (CPU, RAM, disk)
- [ ] Identify slow queries
- [ ] Review and optimize bottlenecks

### 4. Refactoring
- [ ] Identify tech debt
- [ ] Plan refactoring scope (không quá lớn 1 lần)
- [ ] Viết tests trước khi refactor
- [ ] Refactor step-by-step
- [ ] Verify tests vẫn pass sau refactor

### 5. Version Management
- [ ] Theo Semantic Versioning (MAJOR.MINOR.PATCH)
- [ ] Update CHANGELOG.md
- [ ] Tag release trong Git
- [ ] Tạo release notes

---

## Hướng dẫn chi tiết

### Bug Severity Classification

| Severity | Mô tả | Response Time | Ví dụ |
|----------|--------|---------------|-------|
| **Critical** | Hệ thống down, data loss | Ngay lập tức | Server crash, security breach |
| **High** | Feature chính không hoạt động | < 4 giờ | Login failed, payment error |
| **Medium** | Feature phụ bị lỗi, có workaround | < 1 ngày | Filter không hoạt động, UI lỗi |
| **Low** | Cosmetic, minor issue | Next release | Typo, alignment issue |

### Template Bug Report

```markdown
## Bug Report

**Title:** [Mô tả ngắn]
**Severity:** Critical / High / Medium / Low
**Reporter:** [Ai report]
**Environment:** [Production / Staging / Dev]

### Steps to Reproduce
1. Đi tới trang [URL]
2. Nhấn nút [button]
3. Nhập [input]
4. Submit form

### Expected Behavior
[Mô tả kết quả mong đợi]

### Actual Behavior
[Mô tả kết quả thực tế]

### Screenshots / Logs
[Đính kèm nếu có]

### Root Cause (sau khi phân tích)
[Mô tả nguyên nhân gốc]

### Fix
[Mô tả cách fix]
```

### Semantic Versioning

```
MAJOR.MINOR.PATCH
  │      │     │
  │      │     └── Bug fixes, backward compatible
  │      └──────── New features, backward compatible
  └─────────────── Breaking changes

Ví dụ:
1.0.0  →  Initial release
1.0.1  →  Bug fix
1.1.0  →  New feature (backward compatible)
2.0.0  →  Breaking change
```

### Refactoring Priority Matrix

| Impact | Effort Low | Effort High |
|--------|-----------|------------|
| **High** | ✅ Làm ngay | 📋 Lên kế hoạch |
| **Low** | ✅ Làm khi có time | ❌ Bỏ qua / defer |

### Dependency Update Strategy

```
1. Check outdated:
   npm outdated

2. Update patch versions first (safe):
   npm update

3. Update minor versions (review changelog):
   npx npm-check-updates -t minor -u
   npm install

4. Update major versions (careful, may break):
   npx npm-check-updates -t major
   # Review each one manually

5. Run full test suite after update:
   npm test

6. Check for security issues:
   npm audit
   npm audit fix
```

---

### 6. Database Backup & Recovery
- [ ] Setup automated database backups
- [ ] Xác định backup frequency (daily / hourly)
- [ ] Test backup restore procedure
- [ ] Lưu backup ở nơi khác server chính (offsite)
- [ ] Document restore steps

### Backup Strategy

```bash
# PostgreSQL — Automated backup script
#!/bin/bash
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
DB_NAME="myapp_production"
BACKUP_DIR="/backups"
RETENTION_DAYS=30

# Create backup
pg_dump $DB_NAME | gzip > "$BACKUP_DIR/${DB_NAME}_${TIMESTAMP}.sql.gz"

# Delete old backups
find $BACKUP_DIR -name "*.sql.gz" -mtime +$RETENTION_DAYS -delete

echo "Backup completed: ${DB_NAME}_${TIMESTAMP}.sql.gz"
```

```bash
# Restore from backup
gunzip < backup_file.sql.gz | psql $DB_NAME

# MySQL backup
mysqldump -u root -p myapp_production | gzip > backup.sql.gz
```

### Disaster Recovery Checklist

```
□ Backup tự động chạy hàng ngày
□ Đã test restore thành công ít nhất 1 lần
□ Backup lưu ở offsite (S3, Google Cloud Storage...)
□ Có monitoring cho backup failures
□ Document: ai có quyền restore, restore mất bao lâu
□ RPO (Recovery Point Objective): chấp nhận mất data bao lâu?
□ RTO (Recovery Time Objective): downtime tối đa bao lâu?
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Fix bug mà không viết test | Luôn viết test cho bug đã fix |
| Update tất cả dependencies 1 lần | Update từng bước, test sau mỗi update |
| Big bang refactoring | Refactor nhỏ, từng bước, có tests |
| Ignore security vulnerabilities | Xử lý security issues ưu tiên cao |
| Không track tech debt | Maintain danh sách tech debt items |

---

## Liên kết

- **Trước đó:** [09-Documentation](../09-documentation/SKILL.md)
- **Quay lại:** [WORKFLOW](../../WORKFLOW.md)
- **Skill liên quan:** [performance-optimization](../../skills/performance-optimization/SKILL.md), [security](../../skills/security/SKILL.md), [logging-monitoring](../../skills/logging-monitoring/SKILL.md)
