---
name: Implementation
description: Triển khai code - coding standards, patterns, cấu trúc module, naming conventions
---

# 05 — Implementation (Triển khai code)

## Tổng quan

Kit này hướng dẫn quy trình viết code: từ coding standards, naming conventions, design patterns đến cấu trúc module. Đảm bảo code sạch, maintainable và consistent.

**Khi nào sử dụng:** Trong giai đoạn xây dựng (Phase 3 - Build).

---

## Checklist

### 1. Trước khi code
- [ ] Review design document (từ Kit 04)
- [ ] Xác định thứ tự implement (dependencies first)
- [ ] Tạo branch mới theo git workflow
- [ ] Đảm bảo dev environment chạy OK

### 2. Coding Standards
- [ ] Tuân theo naming conventions
- [ ] Áp dụng design patterns phù hợp
- [ ] Viết code DRY (Don't Repeat Yourself)
- [ ] Giữ functions ngắn gọn (< 30 lines)
- [ ] Giữ files có kích thước hợp lý (< 300 lines)

### 3. Implement theo thứ tự
- [ ] **Layer 1:** Models / Database schema
- [ ] **Layer 2:** Services / Business logic
- [ ] **Layer 3:** Controllers / Route handlers
- [ ] **Layer 4:** API routes / Middleware
- [ ] **Layer 5:** Frontend components
- [ ] **Layer 6:** Integration & wiring

### 4. Code Quality
- [ ] Xử lý errors properly (try-catch, error boundaries)
- [ ] Viết input validation
- [ ] Xử lý edge cases
- [ ] Add logging ở các điểm quan trọng
- [ ] Không hard-code values (dùng constants/config)

### 5. Sau khi code
- [ ] Chạy linter, fix warnings
- [ ] Self-review code trước khi commit
- [ ] Viết commit message rõ ràng
- [ ] Chạy tests (nếu có)

---

## Hướng dẫn chi tiết

### Naming Conventions

| Loại | Convention | Ví dụ |
|------|-----------|-------|
| **Variables** | camelCase | `userName`, `isActive`, `totalCount` |
| **Functions** | camelCase, bắt đầu bằng verb | `getUser()`, `createOrder()`, `isValid()` |
| **Classes** | PascalCase | `UserService`, `OrderController` |
| **Constants** | UPPER_SNAKE | `MAX_RETRIES`, `API_BASE_URL` |
| **Files** | kebab-case hoặc camelCase | `user-service.ts`, `userController.js` |
| **Database tables** | snake_case, số nhiều | `users`, `order_items` |
| **Database columns** | snake_case | `created_at`, `full_name` |
| **API endpoints** | kebab-case, số nhiều | `/api/order-items` |
| **CSS classes** | BEM hoặc kebab-case | `card__title`, `btn-primary` |
| **Env variables** | UPPER_SNAKE | `DB_HOST`, `API_KEY` |

### Design Patterns thường dùng

#### Repository Pattern (Data Access)
```javascript
// Tách data access khỏi business logic
class UserRepository {
  async findById(id) {
    return await db.query('SELECT * FROM users WHERE id = $1', [id]);
  }
  
  async create(userData) {
    return await db.query(
      'INSERT INTO users (email, name) VALUES ($1, $2) RETURNING *',
      [userData.email, userData.name]
    );
  }
}
```

#### Service Pattern (Business Logic)
```javascript
// Business logic tách biệt khỏi controllers
class UserService {
  constructor(userRepo, emailService) {
    this.userRepo = userRepo;
    this.emailService = emailService;
  }
  
  async registerUser(data) {
    // Validation
    if (await this.userRepo.findByEmail(data.email)) {
      throw new ConflictError('Email already exists');
    }
    
    // Business logic
    const hashedPassword = await hash(data.password);
    const user = await this.userRepo.create({ ...data, password: hashedPassword });
    
    // Side effects
    await this.emailService.sendWelcome(user.email);
    
    return user;
  }
}
```

#### Controller Pattern (Request Handling)
```javascript
// Controller chỉ xử lý HTTP request/response
class UserController {
  constructor(userService) {
    this.userService = userService;
  }
  
  async register(req, res, next) {
    try {
      const user = await this.userService.registerUser(req.body);
      res.status(201).json({ success: true, data: user });
    } catch (error) {
      next(error);
    }
  }
}
```

#### Middleware Pattern
```javascript
// Middleware cho cross-cutting concerns
const authMiddleware = async (req, res, next) => {
  try {
    const token = req.headers.authorization?.split(' ')[1];
    if (!token) throw new UnauthorizedError('Token required');
    
    const payload = verifyToken(token);
    req.user = payload;
    next();
  } catch (error) {
    next(error);
  }
};
```

### Thứ tự Implement chuẩn

```
1. Config & Utils
   ├── Database connection
   ├── Environment config
   └── Helper functions
         │
2. Models & Migrations
   ├── Database schemas
   ├── Model definitions
   └── Seed data
         │
3. Repositories / Data Access
   ├── CRUD operations
   └── Complex queries
         │
4. Services / Business Logic
   ├── Core business rules
   ├── Validation logic
   └── External service integration
         │
5. Controllers / Handlers
   ├── Request parsing
   ├── Response formatting
   └── Error handling
         │
6. Routes & Middleware
   ├── Route definitions
   ├── Auth middleware
   └── Validation middleware
         │
7. Frontend Components
   ├── Layout components
   ├── Page components
   ├── Form components
   └── Shared/UI components
         │
8. Integration
   ├── API client
   ├── State management
   └── Routing
```

### Template Module Structure

```
feature/
├── feature.model.ts        # Data model
├── feature.repository.ts   # Data access
├── feature.service.ts      # Business logic
├── feature.controller.ts   # Request handling
├── feature.routes.ts       # Route definitions
├── feature.validator.ts    # Input validation
├── feature.types.ts        # TypeScript interfaces
└── feature.test.ts         # Tests
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Business logic trong controller | Tách vào service layer |
| SQL queries trong controller | Dùng repository pattern |
| Giant functions (>50 lines) | Chia nhỏ, mỗi function 1 việc |
| Không xử lý errors | Try-catch + error middleware |
| Magic numbers/strings | Dùng constants có tên rõ ràng |
| Copy-paste code | Extract thành shared functions |
| Không validate input | Validate ở boundary (controller/form) |
| Console.log everywhere | Dùng proper logging library |

---

## Liên kết

- **Trước đó:** [04-System Design](../04-system-design/SKILL.md)
- **Tiếp theo:** [06-Testing](../06-testing/SKILL.md)
- **Skill liên quan:** [error-handling](../../skills/error-handling/SKILL.md), [backend-development](../../skills/backend-development/SKILL.md), [frontend-development](../../skills/frontend-development/SKILL.md), [data-validation](../../skills/data-validation/SKILL.md)

