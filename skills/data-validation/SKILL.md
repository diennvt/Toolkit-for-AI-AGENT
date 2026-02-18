---
name: Data Validation
description: Data validation - Zod, Joi, Pydantic, validation patterns, sanitization
---

# Data Validation (Kiểm tra dữ liệu)

## Tổng quan

Skill kit này hướng dẫn validate và sanitize dữ liệu đầu vào: chọn library, validation patterns, custom validators, và best practices.

---

## Checklist

- [ ] Chọn validation library (Zod / Joi / Pydantic)
- [ ] Validate tất cả input từ client
- [ ] Define schemas cho mọi endpoint
- [ ] Sanitize input (trim, lowercase email...)
- [ ] Custom validators cho business rules
- [ ] Reusable validation schemas
- [ ] Error messages rõ ràng cho user

---

## Hướng dẫn chi tiết

### Chọn Library

| Library | Language | Ưu điểm |
|---------|----------|----------|
| **Zod** | TypeScript | Type inference, nhẹ, phổ biến nhất |
| **Joi** | JavaScript | Mature, nhiều features |
| **Yup** | JavaScript | Tốt cho form validation |
| **Pydantic** | Python | Built-in FastAPI, mạnh mẽ |
| **class-validator** | TypeScript | Decorator-based (NestJS) |

### Zod (TypeScript — Recommended)

```typescript
import { z } from 'zod';

// ==========================================
// Basic Schemas
// ==========================================
const emailSchema = z.string().email().toLowerCase().trim();
const passwordSchema = z.string().min(8).max(128);
const uuidSchema = z.string().uuid();
const phoneSchema = z.string().regex(/^(\+84|0)\d{9,10}$/);

// ==========================================
// Entity Schemas
// ==========================================
const createUserSchema = z.object({
  email: emailSchema,
  password: passwordSchema,
  name: z.string().min(2).max(100).trim(),
  age: z.number().int().min(0).max(150).optional(),
  role: z.enum(['user', 'admin', 'moderator']).default('user'),
});

const updateUserSchema = createUserSchema.partial().omit({ email: true });
// partial() → tất cả fields optional, omit() → bỏ email

// ==========================================
// Query Schemas
// ==========================================
const paginationSchema = z.object({
  page: z.coerce.number().int().min(1).default(1),
  limit: z.coerce.number().int().min(1).max(100).default(20),
  sort: z.enum(['created_at', 'name', 'price']).default('created_at'),
  order: z.enum(['asc', 'desc']).default('desc'),
});

const searchSchema = paginationSchema.extend({
  q: z.string().min(1).max(100).optional(),
  category: z.string().uuid().optional(),
  minPrice: z.coerce.number().min(0).optional(),
  maxPrice: z.coerce.number().min(0).optional(),
});

// ==========================================
// Type Inference (no duplicate types!)
// ==========================================
type CreateUser = z.infer<typeof createUserSchema>;
// → { email: string; password: string; name: string; age?: number; role: 'user' | 'admin' | 'moderator' }

type SearchParams = z.infer<typeof searchSchema>;
```

### Validation Middleware (Express)

```typescript
// middleware/validate.ts
import { ZodSchema } from 'zod';

const validate = (schema: ZodSchema, source: 'body' | 'query' | 'params' = 'body') => {
  return (req, res, next) => {
    const result = schema.safeParse(req[source]);
    if (!result.success) {
      const details = result.error.errors.map(e => ({
        field: e.path.join('.'),
        message: e.message,
      }));
      return res.status(400).json({
        success: false,
        error: { code: 'VALIDATION_ERROR', message: 'Invalid input', details }
      });
    }
    req[source] = result.data; // cleaned + typed data
    next();
  };
};

// Usage
router.post('/users', validate(createUserSchema), userController.create);
router.get('/products', validate(searchSchema, 'query'), productController.list);
router.get('/users/:id', validate(z.object({ id: uuidSchema }), 'params'), userController.get);
```

### Custom Validators

```typescript
// Reusable refinements
const passwordWithRules = z.string()
  .min(8)
  .max(128)
  .refine(val => /[A-Z]/.test(val), 'Must contain uppercase letter')
  .refine(val => /[0-9]/.test(val), 'Must contain number')
  .refine(val => /[!@#$%]/.test(val), 'Must contain special character');

// Cross-field validation
const dateRangeSchema = z.object({
  startDate: z.coerce.date(),
  endDate: z.coerce.date(),
}).refine(
  data => data.endDate > data.startDate,
  { message: 'End date must be after start date', path: ['endDate'] }
);

// Transform + validate
const slugSchema = z.string()
  .transform(val => val.toLowerCase().replace(/\s+/g, '-').replace(/[^a-z0-9-]/g, ''))
  .pipe(z.string().min(1).max(200));
```

---

## Python / Pydantic

```python
from pydantic import BaseModel, EmailStr, Field, field_validator
from typing import Optional
from enum import Enum

class Role(str, Enum):
    user = "user"
    admin = "admin"
    moderator = "moderator"

class UserCreate(BaseModel):
    email: EmailStr
    password: str = Field(min_length=8, max_length=128)
    name: str = Field(min_length=2, max_length=100)
    age: Optional[int] = Field(None, ge=0, le=150)
    role: Role = Role.user

    @field_validator('name')
    @classmethod
    def name_must_be_clean(cls, v):
        return v.strip()

    @field_validator('password')
    @classmethod
    def password_complexity(cls, v):
        if not any(c.isupper() for c in v):
            raise ValueError('Must contain uppercase letter')
        if not any(c.isdigit() for c in v):
            raise ValueError('Must contain number')
        return v

class UserUpdate(BaseModel):
    name: Optional[str] = Field(None, min_length=2, max_length=100)
    age: Optional[int] = Field(None, ge=0, le=150)

class PaginationParams(BaseModel):
    page: int = Field(1, ge=1)
    limit: int = Field(20, ge=1, le=100)
    sort: str = "created_at"
    order: str = "desc"

# FastAPI tự động validate request body = Pydantic model
@router.post("/users")
async def create_user(data: UserCreate):  # ← auto-validated
    ...
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Validate ở frontend, không ở backend | Validate ở CẢ HAI nơi |
| Tin tưởng client input | Validate + sanitize mọi input |
| Viết validation logic thủ công | Dùng library (Zod, Pydantic) |
| Generic error messages | Chi tiết field nào sai, lỗi gì |
| Duplicate types + schemas | Zod `infer` / Pydantic auto-generates |
| Không validate query params | Validate body, query, AND params |

---

## Liên kết

- **Skill liên quan:** [backend-development](../backend-development/SKILL.md), [api-design](../api-design/SKILL.md), [security](../security/SKILL.md)
