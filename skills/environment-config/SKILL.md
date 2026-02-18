---
name: Environment Config
description: Environment Config - .env management, secrets, config per environment, 12-factor app
---

# Environment Configuration

## Tổng quan

Skill kit này hướng dẫn quản lý environment configuration: .env files, secrets management, config theo môi trường, và 12-factor app principles.

---

## Checklist

- [ ] Tạo `.env.example` với tất cả variables cần thiết
- [ ] Setup config module đọc từ env vars
- [ ] Config khác nhau cho dev/staging/prod
- [ ] Không commit `.env` lên Git
- [ ] Validate env vars khi app khởi động
- [ ] Secrets management cho production

---

## Hướng dẫn chi tiết

### Config Module

```typescript
// config/environment.ts
import { z } from 'zod';

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'staging', 'production']).default('development'),
  PORT: z.coerce.number().default(3000),

  // Database
  DB_HOST: z.string(),
  DB_PORT: z.coerce.number().default(5432),
  DB_NAME: z.string(),
  DB_USER: z.string(),
  DB_PASSWORD: z.string(),

  // JWT
  JWT_SECRET: z.string().min(32),
  JWT_EXPIRES_IN: z.string().default('15m'),

  // External services
  SMTP_HOST: z.string().optional(),
  REDIS_URL: z.string().optional(),
  S3_BUCKET: z.string().optional(),
});

// Validate on startup — fail fast if missing
const parsed = envSchema.safeParse(process.env);
if (!parsed.success) {
  console.error('❌ Invalid environment variables:', parsed.error.format());
  process.exit(1);
}

export const env = parsed.data;

// Usage: import { env } from './config/environment';
// env.DB_HOST, env.PORT, etc.
```

### Template .env.example

```env
# ==========================================
# App Configuration
# ==========================================
NODE_ENV=development
PORT=3000
APP_URL=http://localhost:3000

# ==========================================
# Database
# ==========================================
DB_HOST=localhost
DB_PORT=5432
DB_NAME=myapp_dev
DB_USER=postgres
DB_PASSWORD=postgres

# ==========================================
# Authentication
# ==========================================
JWT_SECRET=your-secret-key-at-least-32-characters-long
JWT_EXPIRES_IN=15m
REFRESH_TOKEN_EXPIRES_IN=7d

# ==========================================
# External Services (optional)
# ==========================================
# SMTP_HOST=smtp.gmail.com
# SMTP_PORT=587
# SMTP_USER=
# SMTP_PASS=

# REDIS_URL=redis://localhost:6379

# AWS_ACCESS_KEY_ID=
# AWS_SECRET_ACCESS_KEY=
# AWS_S3_BUCKET=
# AWS_REGION=
```

### Per-environment Config

```
.env                  → Default (shared, commits OK nếu không có secrets)
.env.development      → Dev overrides
.env.staging          → Staging overrides (KHÔNG commit)
.env.production       → Production overrides (KHÔNG commit)
.env.local            → Local overrides (KHÔNG commit)
.env.example          → Template cho team (PHẢI commit)
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Commit `.env` lên Git | `.env` trong `.gitignore`, commit `.env.example` |
| Không validate env vars | Validate + fail fast khi startup |
| Hard-code secrets | Luôn dùng env vars |
| Cùng DB cho dev và prod | Tách database cho mỗi environment |
| Optional secrets cho production | Required validation cho production |

---

## Liên kết

- **Process liên quan:** [01-Project Init](../../processes/01-project-init/SKILL.md), [08-Deployment](../../processes/08-deployment/SKILL.md)
- **Skill liên quan:** [docker-containerization](../docker-containerization/SKILL.md), [security](../security/SKILL.md)
