---
name: Testing
description: Kiểm thử - unit test, integration test, e2e test, test coverage strategy
---

# 06 — Testing (Kiểm thử)

## Tổng quan

Kit này hướng dẫn chiến lược kiểm thử phần mềm: từ unit test, integration test đến e2e test. Đảm bảo code hoạt động đúng, phát hiện bugs sớm.

**Khi nào sử dụng:** Song song hoặc ngay sau khi implement.

---

## Checklist

### 1. Chiến lược Testing
- [ ] Xác định testing pyramid cho dự án
- [ ] Chọn testing framework (Jest, Vitest, Pytest, JUnit...)
- [ ] Setup test environment (test database, mock services)
- [ ] Xác định test coverage target (mục tiêu: ≥ 80%)

### 2. Unit Tests
- [ ] Test tất cả business logic (services)
- [ ] Test utility functions
- [ ] Test validation logic
- [ ] Test edge cases và error paths
- [ ] Mock external dependencies

### 3. Integration Tests
- [ ] Test API endpoints (request → response)
- [ ] Test database operations
- [ ] Test authentication flow
- [ ] Test middleware chain

### 4. E2E Tests (nếu có UI)
- [ ] Test critical user flows
- [ ] Test form submissions
- [ ] Test navigation
- [ ] Test responsive behavior

### 5. Test Maintenance
- [ ] Tests chạy đúng trong CI/CD
- [ ] Không có flaky tests
- [ ] Test data được cleanup sau mỗi test

---

## Hướng dẫn chi tiết

### Testing Pyramid

```
        ╱╲
       ╱  ╲        E2E Tests (ít nhất, chậm nhất)
      ╱ E2E╲       → Test toàn bộ user flow
     ╱──────╲
    ╱        ╲     Integration Tests (vừa phải)
   ╱Integration╲   → Test nhiều components cùng lúc
  ╱──────────────╲
 ╱                ╲ Unit Tests (nhiều nhất, nhanh nhất)
╱   Unit Tests     ╲→ Test từng function/class riêng lẻ
╲__________________╱
```

### Unit Test Template

```javascript
// service.test.js
describe('UserService', () => {
  let userService;
  let mockUserRepo;

  beforeEach(() => {
    mockUserRepo = {
      findByEmail: jest.fn(),
      create: jest.fn(),
    };
    userService = new UserService(mockUserRepo);
  });

  describe('registerUser', () => {
    it('should create user with hashed password', async () => {
      // Arrange
      mockUserRepo.findByEmail.mockResolvedValue(null);
      mockUserRepo.create.mockResolvedValue({ id: '1', email: 'test@test.com' });

      // Act
      const result = await userService.registerUser({
        email: 'test@test.com',
        password: 'password123',
      });

      // Assert
      expect(result.id).toBe('1');
      expect(mockUserRepo.create).toHaveBeenCalledWith(
        expect.objectContaining({ email: 'test@test.com' })
      );
    });

    it('should throw error if email already exists', async () => {
      // Arrange
      mockUserRepo.findByEmail.mockResolvedValue({ id: '1' });

      // Act & Assert
      await expect(
        userService.registerUser({ email: 'test@test.com' })
      ).rejects.toThrow('Email already exists');
    });
  });
});
```

### Integration Test Template

```javascript
// api.test.js
describe('POST /api/users', () => {
  beforeAll(async () => {
    await db.migrate.latest();
  });

  afterEach(async () => {
    await db('users').truncate();
  });

  it('should create a new user', async () => {
    const res = await request(app)
      .post('/api/users')
      .send({ email: 'test@test.com', password: 'password123', name: 'Test' })
      .expect(201);

    expect(res.body.success).toBe(true);
    expect(res.body.data.email).toBe('test@test.com');
  });

  it('should return 400 for invalid email', async () => {
    const res = await request(app)
      .post('/api/users')
      .send({ email: 'invalid', password: 'password123' })
      .expect(400);

    expect(res.body.success).toBe(false);
    expect(res.body.error.code).toBe('VALIDATION_ERROR');
  });

  it('should return 409 for duplicate email', async () => {
    await createUser({ email: 'test@test.com' });

    const res = await request(app)
      .post('/api/users')
      .send({ email: 'test@test.com', password: 'password123' })
      .expect(409);

    expect(res.body.error.code).toBe('CONFLICT');
  });
});
```

### Test Cases cần cover

| Category | Test Cases |
|----------|-----------|
| **Happy path** | Input đúng → kết quả đúng |
| **Validation** | Input thiếu/sai → error message rõ ràng |
| **Auth** | Không có token → 401, sai role → 403 |
| **Edge cases** | Empty input, null, undefined, extreme values |
| **Error handling** | DB lỗi, external service lỗi → graceful error |
| **Boundary** | Min/max values, empty lists, pagination limits |

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Không viết test | Ít nhất test business logic và API |
| Test phụ thuộc vào nhau | Mỗi test độc lập, có setup/teardown |
| Chỉ test happy path | Test cả error paths và edge cases |
| Test quá chi tiết (implementation) | Test behavior, không test implementation |
| Hard-code test data | Dùng factory/fixture functions |
| Skip flaky tests | Fix root cause hoặc remove |

---

## Liên kết

- **Trước đó:** [05-Implementation](../05-implementation/SKILL.md)
- **Tiếp theo:** [07-Code Review](../07-code-review/SKILL.md)
- **Skill liên quan:** [testing-tools](../../skills/testing-tools/SKILL.md), [error-handling](../../skills/error-handling/SKILL.md), [data-validation](../../skills/data-validation/SKILL.md)

