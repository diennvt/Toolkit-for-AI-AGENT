---
name: Backend Development
description: Backend - server architecture, middleware, routing, business logic patterns
---

# Backend Development

## Tổng quan

Skill kit này hướng dẫn phát triển backend: server architecture, middleware, routing, và các patterns cho business logic.

---

## Checklist

- [ ] Chọn framework (Express, Fastify, NestJS, Django, FastAPI, Spring Boot...)
- [ ] Setup project structure (layered architecture)
- [ ] Cấu hình database connection
- [ ] Setup middleware chain (cors, helmet, logger, auth...)
- [ ] Implement routing
- [ ] Implement controllers → services → repositories
- [ ] Input validation
- [ ] Error handling middleware
- [ ] Logging setup
- [ ] Health check endpoint

---

## Hướng dẫn chi tiết

### Layered Architecture

```
Request → Middleware → Controller → Service → Repository → Database
                          │              │           │
                       Validate     Business     Data Access
                       Parse req    Logic        Queries
                       Format res   Rules        ORM calls
```

### Project Structure

```
src/
├── config/
│   ├── database.ts          # DB connection
│   ├── environment.ts       # Env vars
│   └── logger.ts            # Logger setup
├── middleware/
│   ├── auth.ts              # JWT verification
│   ├── validate.ts          # Request validation
│   ├── errorHandler.ts      # Global error handler
│   └── rateLimiter.ts       # Rate limiting
├── modules/
│   └── [feature]/
│       ├── feature.model.ts
│       ├── feature.repository.ts
│       ├── feature.service.ts
│       ├── feature.controller.ts
│       ├── feature.routes.ts
│       ├── feature.validator.ts
│       └── feature.test.ts
├── shared/
│   ├── errors/              # Custom error classes
│   ├── utils/               # Helper functions
│   └── types/               # Shared types
├── app.ts                   # Express app setup
└── server.ts                # Server entry point
```

### Express App Setup Template

```typescript
// app.ts
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import morgan from 'morgan';
import { errorHandler } from './middleware/errorHandler';
import { notFound } from './middleware/notFound';
import routes from './routes';

const app = express();

// Security middleware
app.use(helmet());
app.use(cors({ origin: process.env.CORS_ORIGIN }));

// Parsing middleware
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true }));

// Logging
app.use(morgan('combined'));

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'ok', timestamp: new Date().toISOString() });
});

// API routes
app.use('/api/v1', routes);

// Error handling (must be last)
app.use(notFound);
app.use(errorHandler);

export default app;
```

### Error Handling Pattern

```typescript
// shared/errors/AppError.ts
class AppError extends Error {
  constructor(
    public statusCode: number,
    public code: string,
    message: string,
    public details?: any
  ) {
    super(message);
  }
}

class NotFoundError extends AppError {
  constructor(resource: string) {
    super(404, 'NOT_FOUND', `${resource} not found`);
  }
}

class ValidationError extends AppError {
  constructor(details: any[]) {
    super(400, 'VALIDATION_ERROR', 'Invalid input', details);
  }
}

class UnauthorizedError extends AppError {
  constructor(message = 'Unauthorized') {
    super(401, 'UNAUTHORIZED', message);
  }
}

// middleware/errorHandler.ts
const errorHandler = (err, req, res, next) => {
  const statusCode = err.statusCode || 500;
  const code = err.code || 'INTERNAL_ERROR';

  // Log error
  logger.error({ err, req: { method: req.method, url: req.url } });

  res.status(statusCode).json({
    success: false,
    error: {
      code,
      message: err.message,
      ...(err.details && { details: err.details }),
    },
  });
};
```

### Input Validation (with Zod)

```typescript
// feature.validator.ts
import { z } from 'zod';

export const createUserSchema = z.object({
  email: z.string().email('Invalid email'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
  name: z.string().min(2, 'Name must be at least 2 characters'),
});

// middleware/validate.ts
const validate = (schema) => (req, res, next) => {
  const result = schema.safeParse(req.body);
  if (!result.success) {
    const details = result.error.errors.map(e => ({
      field: e.path.join('.'),
      message: e.message,
    }));
    throw new ValidationError(details);
  }
  req.body = result.data; // cleaned data
  next();
};

// Usage in routes
router.post('/users', validate(createUserSchema), userController.create);
```

### Middleware Chain chuẩn

```
Request Flow:
1. CORS          → Cross-origin handling
2. Helmet        → Security headers
3. Body Parser   → Parse JSON/form data
4. Morgan        → Request logging
5. Rate Limiter  → Prevent abuse
6. Auth          → Verify JWT token
7. Validate      → Validate request body
8. Controller    → Handle request
9. Error Handler → Catch & format errors
```

---

## Python / FastAPI

### Project Structure

```
app/
├── core/
│   ├── config.py              # Settings (Pydantic)
│   ├── database.py            # DB session
│   └── security.py            # JWT, hashing
├── models/                    # SQLAlchemy models
│   ├── user.py
│   └── product.py
├── schemas/                   # Pydantic schemas (request/response)
│   ├── user.py
│   └── product.py
├── api/
│   ├── deps.py                # Dependencies (get_db, get_current_user)
│   └── v1/
│       ├── router.py
│       ├── auth.py
│       └── products.py
├── services/                  # Business logic
├── repositories/              # Data access
└── main.py                    # Entry point
```

### FastAPI App Setup

```python
# main.py
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.api.v1.router import api_router
from app.core.config import settings

app = FastAPI(title=settings.APP_NAME, version="1.0.0")

app.add_middleware(
    CORSMiddleware,
    allow_origins=[settings.CORS_ORIGIN],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

app.include_router(api_router, prefix="/api/v1")

@app.get("/health")
async def health():
    return {"status": "ok"}
```

### Error Handling (FastAPI)

```python
from fastapi import HTTPException, Request
from fastapi.responses import JSONResponse

class AppException(Exception):
    def __init__(self, status_code: int, code: str, message: str):
        self.status_code = status_code
        self.code = code
        self.message = message

@app.exception_handler(AppException)
async def app_exception_handler(request: Request, exc: AppException):
    return JSONResponse(
        status_code=exc.status_code,
        content={"success": False, "error": {"code": exc.code, "message": exc.message}},
    )
```

### Dependency Injection

```python
# api/deps.py
from fastapi import Depends, HTTPException
from sqlalchemy.orm import Session

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

def get_current_user(
    token: str = Depends(oauth2_scheme),
    db: Session = Depends(get_db)
) -> User:
    payload = verify_token(token)
    user = db.query(User).filter(User.id == payload["sub"]).first()
    if not user:
        raise HTTPException(status_code=401, detail="User not found")
    return user

# Usage in route
@router.get("/profile")
async def get_profile(current_user: User = Depends(get_current_user)):
    return {"success": True, "data": current_user}
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Logic trong controller | Tách vào service layer |
| Không validate input | Validate ở mọi boundary |
| `try-catch` ở mọi controller | Dùng global error handler |
| Không có health check | Luôn có `/health` endpoint |
| Blocking I/O trên main thread | Dùng async/await, worker threads |
| Secrets trong source code | Dùng environment variables |

---

## Liên kết

- **Process liên quan:** [05-Implementation](../../processes/05-implementation/SKILL.md)
- **Skill liên quan:** [api-design](../api-design/SKILL.md), [authentication](../authentication/SKILL.md), [error-handling](../error-handling/SKILL.md)
