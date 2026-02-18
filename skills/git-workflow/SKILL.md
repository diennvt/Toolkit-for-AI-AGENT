---
name: Git Workflow
description: Git - branching strategy, commit conventions, merge/rebase, conflict resolution
---

# Git Workflow

## Tổng quan

Skill kit này hướng dẫn Git workflow chuẩn: branching strategy, commit conventions, và conflict resolution.

---

## Checklist

- [ ] Chọn branching strategy (Git Flow / GitHub Flow / Trunk-based)
- [ ] Setup branch protection rules
- [ ] Áp dụng commit message conventions
- [ ] Thiết lập PR/MR template
- [ ] Setup pre-commit hooks

---

## Hướng dẫn chi tiết

### Branching Strategy

```
GitHub Flow (recommended for most projects):

main (production-ready)
 │
 ├── feature/user-auth        ← Feature branch
 ├── feature/product-crud     ← Feature branch
 ├── fix/login-bug            ← Bugfix branch
 └── hotfix/security-patch    ← Hotfix branch

Branch Naming:
  feature/[short-description]    → New feature
  fix/[short-description]        → Bug fix
  hotfix/[short-description]     → Urgent production fix
  refactor/[short-description]   → Code refactoring
  docs/[short-description]       → Documentation
```

### Commit Message Convention (Conventional Commits)

```
Format: <type>(<scope>): <description>

Types:
  feat:     New feature
  fix:      Bug fix
  docs:     Documentation
  style:    Formatting (no code change)
  refactor: Code restructuring
  test:     Adding/updating tests
  chore:    Build/tooling changes

Examples:
  feat(auth): add JWT refresh token
  fix(cart): fix total calculation for discounts
  docs(api): add endpoint documentation
  refactor(user): extract validation to middleware
  test(order): add integration tests for checkout
```

### Git Workflow Steps

```bash
# 1. Create feature branch
git checkout main
git pull origin main
git checkout -b feature/user-auth

# 2. Work on feature (small, frequent commits)
git add .
git commit -m "feat(auth): add login endpoint"
git commit -m "feat(auth): add JWT token generation"
git commit -m "test(auth): add login integration tests"

# 3. Push and create PR
git push origin feature/user-auth
# Create Pull Request on GitHub

# 4. After PR approved, merge
git checkout main
git pull origin main
git merge feature/user-auth   # or squash merge
git push origin main

# 5. Cleanup
git branch -d feature/user-auth
git push origin --delete feature/user-auth
```

### .gitignore Template

```gitignore
# Dependencies
node_modules/
vendor/
venv/

# Build
dist/
build/
*.pyc
__pycache__/

# Environment
.env
.env.local
.env.*.local

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Testing
coverage/
.pytest_cache/
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Commit trực tiếp vào main | Dùng feature branches + PR |
| Commit message mơ hồ ("fix bug") | Conventional commits rõ ràng |
| Giant commit (1000+ lines) | Small, focused commits |
| Force push shared branches | Chỉ force push branch cá nhân |
| Không pull trước khi push | `git pull --rebase` trước |

---

## Liên kết

- **Process liên quan:** [01-Project Init](../../processes/01-project-init/SKILL.md)
- **Skill liên quan:** [ci-cd](../ci-cd/SKILL.md)
