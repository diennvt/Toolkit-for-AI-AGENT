---
name: Authentication
description: Xác thực & phân quyền - JWT, OAuth, session management, RBAC, password hashing
---

# Authentication & Authorization

## Tổng quan

Skill kit này hướng dẫn implement authentication (xác thực) và authorization (phân quyền): JWT, OAuth, password hashing, role-based access control.

---

## Checklist

- [ ] Chọn auth strategy (JWT / Session / OAuth)
- [ ] Implement password hashing (bcrypt)
- [ ] Implement registration flow
- [ ] Implement login flow
- [ ] Implement JWT (access token + refresh token)
- [ ] Implement auth middleware
- [ ] Implement role-based access control (RBAC)
- [ ] Implement logout / token revocation
- [ ] Implement password reset flow
- [ ] Secure token storage (frontend)

---

## Hướng dẫn chi tiết

### Auth Flow Overview

```
Registration:
User → POST /auth/register → Hash password → Save to DB → Return user

Login:
User → POST /auth/login → Verify password → Generate JWT → Return tokens

Protected Request:
User → GET /api/resource → Auth middleware → Verify JWT → Controller

Token Refresh:
User → POST /auth/refresh → Verify refresh token → New access token
```

### JWT Implementation

```typescript
// config/jwt.ts
import jwt from 'jsonwebtoken';

const JWT_SECRET = process.env.JWT_SECRET!;
const JWT_EXPIRES_IN = '15m';      // Access token: short-lived
const REFRESH_EXPIRES_IN = '7d';    // Refresh token: long-lived

export const generateTokens = (userId: string, role: string) => {
  const accessToken = jwt.sign(
    { userId, role },
    JWT_SECRET,
    { expiresIn: JWT_EXPIRES_IN }
  );

  const refreshToken = jwt.sign(
    { userId, type: 'refresh' },
    JWT_SECRET,
    { expiresIn: REFRESH_EXPIRES_IN }
  );

  return { accessToken, refreshToken };
};

export const verifyToken = (token: string) => {
  return jwt.verify(token, JWT_SECRET);
};
```

### Password Hashing

```typescript
import bcrypt from 'bcrypt';

const SALT_ROUNDS = 12;

// Hash password trước khi save
export const hashPassword = async (password: string): Promise<string> => {
  return bcrypt.hash(password, SALT_ROUNDS);
};

// So sánh password khi login
export const comparePassword = async (
  password: string,
  hash: string
): Promise<boolean> => {
  return bcrypt.compare(password, hash);
};
```

### Auth Middleware

```typescript
// middleware/auth.ts
export const authenticate = async (req, res, next) => {
  try {
    const token = req.headers.authorization?.split(' ')[1];
    if (!token) throw new UnauthorizedError('Token required');

    const payload = verifyToken(token);
    req.user = payload; // { userId, role }
    next();
  } catch (error) {
    if (error.name === 'TokenExpiredError') {
      next(new UnauthorizedError('Token expired'));
    } else {
      next(new UnauthorizedError('Invalid token'));
    }
  }
};

// Role-based authorization
export const authorize = (...roles: string[]) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      throw new ForbiddenError('Insufficient permissions');
    }
    next();
  };
};

// Usage:
router.get('/admin/users', authenticate, authorize('admin'), getUsers);
router.get('/profile', authenticate, getProfile);
```

### Registration & Login Controller

```typescript
// auth.controller.ts
class AuthController {
  async register(req, res) {
    const { email, password, name } = req.body;

    // Check duplicate
    const existing = await userRepo.findByEmail(email);
    if (existing) throw new ConflictError('Email already exists');

    // Hash password
    const hashedPassword = await hashPassword(password);

    // Create user
    const user = await userRepo.create({
      email, password: hashedPassword, name
    });

    // Generate tokens
    const tokens = generateTokens(user.id, user.role);

    res.status(201).json({
      success: true,
      data: { user: sanitizeUser(user), ...tokens }
    });
  }

  async login(req, res) {
    const { email, password } = req.body;

    // Find user
    const user = await userRepo.findByEmail(email);
    if (!user) throw new UnauthorizedError('Invalid credentials');

    // Verify password
    const valid = await comparePassword(password, user.password);
    if (!valid) throw new UnauthorizedError('Invalid credentials');

    // Generate tokens
    const tokens = generateTokens(user.id, user.role);

    res.json({ success: true, data: { user: sanitizeUser(user), ...tokens } });
  }
}
```

### Frontend Token Storage

```typescript
// ✅ GOOD — HttpOnly cookie (most secure)
// Set by server:
res.cookie('refreshToken', refreshToken, {
  httpOnly: true,
  secure: true,
  sameSite: 'strict',
  maxAge: 7 * 24 * 60 * 60 * 1000 // 7 days
});

// ⚠️ ACCEPTABLE — localStorage for access token (short-lived)
localStorage.setItem('accessToken', accessToken);

// ❌ BAD — localStorage for refresh token (long-lived, XSS vulnerable)
```

---

## Python / FastAPI

### JWT + Password Hashing

```python
# core/security.py
from datetime import datetime, timedelta
from passlib.context import CryptContext
from jose import jwt

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
SECRET_KEY = settings.JWT_SECRET
ALGORITHM = "HS256"

def hash_password(password: str) -> str:
    return pwd_context.hash(password)

def verify_password(plain: str, hashed: str) -> bool:
    return pwd_context.verify(plain, hashed)

def create_access_token(data: dict, expires_delta: timedelta = timedelta(minutes=15)):
    to_encode = data.copy()
    to_encode["exp"] = datetime.utcnow() + expires_delta
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

def verify_token(token: str) -> dict:
    return jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
```

### Auth Routes (FastAPI)

```python
# api/v1/auth.py
from fastapi import APIRouter, Depends, HTTPException
from fastapi.security import OAuth2PasswordBearer

router = APIRouter(prefix="/auth", tags=["auth"])
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/api/v1/auth/login")

@router.post("/register")
async def register(data: UserCreate, db: Session = Depends(get_db)):
    if db.query(User).filter(User.email == data.email).first():
        raise HTTPException(409, "Email already exists")
    user = User(email=data.email, password=hash_password(data.password), name=data.name)
    db.add(user)
    db.commit()
    token = create_access_token({"sub": str(user.id), "role": user.role})
    return {"success": True, "data": {"access_token": token}}

@router.post("/login")
async def login(data: UserLogin, db: Session = Depends(get_db)):
    user = db.query(User).filter(User.email == data.email).first()
    if not user or not verify_password(data.password, user.password):
        raise HTTPException(401, "Invalid credentials")
    token = create_access_token({"sub": str(user.id), "role": user.role})
    return {"success": True, "data": {"access_token": token}}
```

### RBAC Dependency

```python
def require_role(*roles: str):
    def role_checker(current_user: User = Depends(get_current_user)):
        if current_user.role not in roles:
            raise HTTPException(403, "Insufficient permissions")
        return current_user
    return role_checker

# Usage
@router.get("/admin/users")
async def list_users(user: User = Depends(require_role("admin"))):
    ...
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| MD5/SHA256 cho password | Dùng bcrypt (cost factor ≥ 12) |
| JWT không hết hạn | Access: 15m, Refresh: 7d |
| Refresh token trong localStorage | HttpOnly cookie cho refresh token |
| Generic "invalid" message | "Invalid credentials" (không tiết lộ field nào sai) |
| Không có rate limiting cho login | Rate limit: max 5 attempts / 15 min |
| RBAC hard-coded trong routes | Tách auth logic vào middleware |

---

## Liên kết

- **Process liên quan:** [05-Implementation](../../processes/05-implementation/SKILL.md)
- **Skill liên quan:** [security](../security/SKILL.md), [api-design](../api-design/SKILL.md)
