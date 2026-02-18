---
name: Docker & Containerization
description: Docker - Dockerfile, docker-compose, multi-stage builds, best practices
---

# Docker & Containerization

## Tổng quan

Skill kit này hướng dẫn sử dụng Docker: Dockerfile, docker-compose, multi-stage builds, và best practices.

---

## Checklist

- [ ] Viết Dockerfile (multi-stage build)
- [ ] Tạo `.dockerignore`
- [ ] Viết `docker-compose.yml` cho development
- [ ] Setup database container
- [ ] Setup volume cho persistent data
- [ ] Environment variables qua docker-compose
- [ ] Health checks
- [ ] Production-ready image

---

## Hướng dẫn chi tiết

### Multi-stage Dockerfile (Node.js)

```dockerfile
# Stage 1: Build
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: Production
FROM node:20-alpine AS runner
WORKDIR /app
RUN addgroup -g 1001 -S appgroup && adduser -S appuser -u 1001
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./
USER appuser
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "dist/index.js"]
```

### Docker Compose (Development)

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
      - DB_HOST=postgres
      - DB_PORT=5432
      - DB_NAME=myapp
      - DB_USER=postgres
      - DB_PASSWORD=postgres
      - REDIS_URL=redis://redis:6379
    volumes:
      - .:/app
      - /app/node_modules
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_started

  postgres:
    image: postgres:16-alpine
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

### .dockerignore

```
node_modules
dist
.git
.env
.env.*
*.log
coverage
.vscode
.idea
Dockerfile
docker-compose*.yml
README.md
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| `FROM node:20` (full image) | `FROM node:20-alpine` (nhỏ hơn 5x) |
| Run as root | Tạo non-root user |
| No `.dockerignore` | Luôn có `.dockerignore` |
| COPY toàn bộ trước npm install | COPY package*.json trước → npm ci → COPY . |
| No health checks | Luôn có HEALTHCHECK |
| Secrets trong Dockerfile | Dùng env vars hoặc Docker secrets |

---

## Liên kết

- **Process liên quan:** [08-Deployment](../../processes/08-deployment/SKILL.md)
- **Skill liên quan:** [ci-cd](../ci-cd/SKILL.md), [environment-config](../environment-config/SKILL.md)
