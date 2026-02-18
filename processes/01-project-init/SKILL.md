---
name: Project Initialization
description: Khởi tạo dự án - chọn tech stack, tạo cấu trúc thư mục, cấu hình ban đầu
---

# 01 — Project Initialization (Khởi tạo dự án)

## Tổng quan

Kit này hướng dẫn khởi tạo dự án từ đầu: xác định tech stack phù hợp, tạo cấu trúc thư mục chuẩn, cài đặt dependencies, và thiết lập các công cụ phát triển (linter, formatter, pre-commit hooks).

**Khi nào sử dụng:** Khi bắt đầu một dự án mới hoàn toàn.

---

## Checklist

### 1. Xác định Tech Stack
- [ ] Xác định loại dự án (web app, mobile, CLI, API, library...)
- [ ] Chọn ngôn ngữ lập trình chính
- [ ] Chọn framework/runtime (React, Next.js, Express, Django, Spring Boot...)
- [ ] Chọn database (PostgreSQL, MySQL, MongoDB, SQLite...)
- [ ] Xác định các service bên thứ ba cần dùng (auth, email, storage...)

### 2. Khởi tạo Project
- [ ] Tạo repository (Git init hoặc clone template)
- [ ] Chạy CLI khởi tạo framework (`npx create-next-app`, `django-admin startproject`...)
- [ ] Tạo `.gitignore` phù hợp
- [ ] Tạo `README.md` ban đầu (tên project, mô tả ngắn)

### 3. Cấu trúc thư mục
- [ ] Tạo cấu trúc thư mục theo best practice của framework
- [ ] Tạo các thư mục cần thiết: `src/`, `tests/`, `docs/`, `scripts/`
- [ ] Tách biệt rõ ràng: components, services, utils, models, routes

### 4. Cài đặt Dependencies
- [ ] Cài dependencies chính (framework, ORM, router...)
- [ ] Cài dev dependencies (testing, linting, formatting...)
- [ ] Lock file tồn tại (`package-lock.json`, `poetry.lock`...)

### 5. Thiết lập Dev Tools
- [ ] Cấu hình linter (ESLint, Pylint, Flake8...)
- [ ] Cấu hình formatter (Prettier, Black, autopep8...)
- [ ] Cấu hình editor (`.editorconfig`, `.vscode/settings.json`)
- [ ] Setup pre-commit hooks (husky, pre-commit...)
- [ ] Cấu hình TypeScript (nếu dùng) — `tsconfig.json`

### 6. Environment Setup
- [ ] Tạo file `.env.example` với giá trị mẫu
- [ ] Cấu hình environment variables
- [ ] Setup scripts (`dev`, `build`, `test`, `lint`)

---

## Hướng dẫn chi tiết

### Chọn Tech Stack theo loại dự án

| Loại dự án | Frontend | Backend | Database | Gợi ý |
|------------|----------|---------|----------|--------|
| Web App (SPA) | React/Vue/Angular | Node.js/Python | PostgreSQL/MongoDB | Next.js hoặc Vite |
| Web App (SSR) | Next.js/Nuxt | Built-in | PostgreSQL | Full-stack framework |
| REST API | — | Express/FastAPI/Spring | PostgreSQL/MySQL | Stateless design |
| Mobile App | React Native/Flutter | Node.js/Python | PostgreSQL | Expo cho prototype |
| CLI Tool | — | Node.js/Python/Go | SQLite (nếu cần) | Commander.js / Click |
| Microservices | — | Node.js/Go/Java | Per-service DB | Docker + Message Queue |

### Cấu trúc thư mục mẫu

#### Node.js / TypeScript Project
```
project-root/
├── src/
│   ├── config/           # Cấu hình app
│   ├── controllers/      # Request handlers
│   ├── middlewares/       # Express middlewares
│   ├── models/           # Database models
│   ├── routes/           # Route definitions
│   ├── services/         # Business logic
│   ├── utils/            # Helper functions
│   ├── types/            # TypeScript type definitions
│   └── index.ts          # Entry point
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── docs/
├── scripts/
├── .env.example
├── .gitignore
├── .eslintrc.json
├── .prettierrc
├── tsconfig.json
├── package.json
└── README.md
```

#### Python / Django Project
```
project-root/
├── apps/
│   └── core/
│       ├── models.py
│       ├── views.py
│       ├── serializers.py
│       ├── urls.py
│       └── tests/
├── config/
│   ├── settings/
│   │   ├── base.py
│   │   ├── development.py
│   │   └── production.py
│   ├── urls.py
│   └── wsgi.py
├── static/
├── templates/
├── tests/
├── manage.py
├── requirements.txt
├── .env.example
└── README.md
```

### Template .gitignore

```gitignore
# Dependencies
node_modules/
venv/
__pycache__/

# Environment
.env
.env.local
.env.production

# Build
dist/
build/
*.pyc

# IDE
.vscode/
.idea/
*.swp

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

### Template .env.example

```env
# App
APP_NAME=my-project
APP_ENV=development
APP_PORT=3000
APP_SECRET=change-me-in-production

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=my_database
DB_USER=postgres
DB_PASSWORD=change-me

# External Services
# SMTP_HOST=
# SMTP_PORT=
# REDIS_URL=
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Không có `.gitignore` → push `node_modules` lên Git | Tạo `.gitignore` ngay từ đầu |
| Hard-code config trong source code | Dùng environment variables |
| Không lock dependencies version | Commit `package-lock.json` / `poetry.lock` |
| Cấu trúc thư mục phẳng, mọi file ở root | Phân chia theo chức năng (MVC, Clean Architecture) |
| Bỏ qua linter/formatter | Setup ESLint + Prettier từ đầu, tránh tranh luận code style |
| Không có script `dev`/`build`/`test` | Luôn tạo npm scripts / Makefile rõ ràng |

---

## Liên kết

- **Tiếp theo:** [02-Requirements Analysis](../02-requirements-analysis/SKILL.md)
- **Skill liên quan:** [git-workflow](../../skills/git-workflow/SKILL.md), [environment-config](../../skills/environment-config/SKILL.md), [docker-containerization](../../skills/docker-containerization/SKILL.md), [data-validation](../../skills/data-validation/SKILL.md)

