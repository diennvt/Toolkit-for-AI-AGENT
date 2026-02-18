---
name: Deployment
description: Triển khai - build, deploy, environment config, rollback strategy
---

# 08 — Deployment (Triển khai)

## Tổng quan

Kit này hướng dẫn quy trình triển khai ứng dụng lên môi trường production: từ build, environment config, deploy strategy đến rollback plan.

**Khi nào sử dụng:** Khi code đã passed review và testing, sẵn sàng deploy.

---

## Checklist

### 1. Pre-deployment
- [ ] All tests pass
- [ ] Code review approved
- [ ] Environment variables configured
- [ ] Database migrations ready
- [ ] Build thành công ở local
- [ ] Documentation updated

### 2. Build
- [ ] Build production bundle
- [ ] Verify build size hợp lý
- [ ] Check for build warnings/errors
- [ ] Assets optimized (images, fonts)

### 3. Environment Setup
- [ ] Production environment variables set
- [ ] Database production credentials configured
- [ ] SSL/TLS certificates ready
- [ ] Domain/DNS configured
- [ ] External service credentials (email, storage...)

### 4. Deploy
- [ ] Run database migrations
- [ ] Deploy application
- [ ] Verify health check endpoint
- [ ] Smoke test critical paths
- [ ] Monitor logs for errors

### 5. Post-deployment
- [ ] Verify all features working
- [ ] Check performance metrics
- [ ] Monitor error rates
- [ ] Notify stakeholders
- [ ] Tag release in Git

---

## Hướng dẫn chi tiết

### Deployment Environments

```
Development  →  Staging  →  Production
   (dev)        (stage)      (prod)

- Dev:     Cho developer test local
- Staging: Giống production, test trước khi deploy
- Prod:    Live, user thực sử dụng
```

### Deploy Strategy

| Strategy | Mô tả | Risk | Khi nào dùng |
|----------|--------|------|-------------|
| **Direct** | Replace trực tiếp | High | Small projects, MVP |
| **Blue-Green** | 2 môi trường, switch traffic | Low | Production apps |
| **Rolling** | Update từng instance | Medium | Microservices |
| **Canary** | Deploy cho % user nhỏ trước | Low | Large-scale apps |

### Hosting Options

| Platform | Tốt cho | Pricing |
|----------|---------|---------|
| **Vercel** | Next.js, React, static sites | Free tier có |
| **Netlify** | Static sites, JAMstack | Free tier có |
| **Railway** | Full-stack apps, databases | Free tier có |
| **Render** | Full-stack apps, databases | Free tier có |
| **AWS** | Enterprise, scale lớn | Pay-as-you-go |
| **DigitalOcean** | VPS, simple deployment | Từ $5/tháng |
| **Fly.io** | Containerized apps | Free tier có |

### Template Deployment Script

```bash
#!/bin/bash
# deploy.sh

set -e  # Exit on error

echo "🚀 Starting deployment..."

# 1. Pull latest code
echo "📥 Pulling latest code..."
git pull origin main

# 2. Install dependencies
echo "📦 Installing dependencies..."
npm ci --production

# 3. Run migrations
echo "🗃️ Running database migrations..."
npm run migrate

# 4. Build
echo "🔨 Building application..."
npm run build

# 5. Restart application
echo "🔄 Restarting application..."
pm2 restart app

# 6. Health check
echo "🏥 Running health check..."
sleep 5
curl -f http://localhost:3000/health || exit 1

echo "✅ Deployment successful!"
```

### Template Docker Deploy

```dockerfile
# Dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
RUN addgroup -g 1001 -S nodejs && adduser -S nextjs -u 1001
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json
USER nextjs
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

### Rollback Plan

```markdown
# Rollback Procedure

## Khi nào rollback?
- [ ] Critical bug phát hiện sau deploy
- [ ] Performance degradation nghiêm trọng
- [ ] Error rate tăng đột biến

## Steps
1. Revert to previous version:
   git revert HEAD
   git push origin main

2. Rollback database (nếu cần):
   npm run migrate:rollback

3. Redeploy previous version:
   ./deploy.sh

4. Verify rollback:
   curl -f http://localhost:3000/health
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Deploy thẳng lên production | Deploy staging trước, verify, rồi production |
| Không có rollback plan | Luôn có kế hoạch rollback |
| Hard-code production secrets | Dùng environment variables |
| Không check health after deploy | Luôn verify health check endpoint |
| Không tag release | Tag mỗi release: `git tag v1.0.0` |

---

## Liên kết

- **Trước đó:** [07-Code Review](../07-code-review/SKILL.md)
- **Tiếp theo:** [09-Documentation](../09-documentation/SKILL.md)
- **Skill liên quan:** [ci-cd](../../skills/ci-cd/SKILL.md), [docker-containerization](../../skills/docker-containerization/SKILL.md), [environment-config](../../skills/environment-config/SKILL.md)
