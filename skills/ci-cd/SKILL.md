---
name: CI/CD
description: CI/CD Pipeline - automated testing, build, staging/production deployment
---

# CI/CD (Continuous Integration / Continuous Deployment)

## Tổng quan

Skill kit này hướng dẫn setup CI/CD pipeline: automated testing, build, deployment, và best practices.

---

## Checklist

- [ ] Chọn CI/CD platform (GitHub Actions, GitLab CI, Jenkins...)
- [ ] Setup automated testing on push/PR
- [ ] Setup automated linting
- [ ] Setup build pipeline
- [ ] Configure staging deployment
- [ ] Configure production deployment
- [ ] Setup environment secrets
- [ ] Setup notifications (Slack, email)

---

## Hướng dẫn chi tiết

### GitHub Actions — Complete Pipeline

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '20'

jobs:
  # Job 1: Lint & Test
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - run: npm ci
      - run: npm run lint
      - run: npm run test -- --coverage

      - uses: actions/upload-artifact@v4
        with:
          name: coverage
          path: coverage/

  # Job 2: Build
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - run: npm ci
      - run: npm run build

      - uses: actions/upload-artifact@v4
        with:
          name: build
          path: dist/

  # Job 3: Deploy to Staging
  deploy-staging:
    needs: build
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: build
          path: dist/
      - name: Deploy to Staging
        run: |
          echo "Deploying to staging..."
          # Add your deployment commands here

  # Job 4: Deploy to Production
  deploy-production:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: build
          path: dist/
      - name: Deploy to Production
        run: |
          echo "Deploying to production..."
          # Add your deployment commands here
```

### Pipeline Flow

```
Push/PR → Lint → Test → Build → Deploy
           │       │       │        │
           │       │       │        ├── develop → staging
           │       │       │        └── main → production
           │       │       │
           │       │       └── Create build artifacts
           │       └── Run unit + integration tests
           └── ESLint / Prettier check
```

### Docker Build & Push

```yaml
# In CI/CD pipeline
- name: Build and Push Docker
  run: |
    docker build -t ${{ secrets.REGISTRY }}/app:${{ github.sha }} .
    docker push ${{ secrets.REGISTRY }}/app:${{ github.sha }}
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| No CI/CD | Setup từ ngày đầu |
| Skip tests trong CI | Test là bắt buộc |
| Secrets hard-coded | Dùng GitHub Secrets / Vault |
| Deploy without build step | Always build → test → deploy |
| No staging environment | Test on staging before production |

---

## Liên kết

- **Process liên quan:** [08-Deployment](../../processes/08-deployment/SKILL.md)
- **Skill liên quan:** [docker-containerization](../docker-containerization/SKILL.md), [git-workflow](../git-workflow/SKILL.md)
