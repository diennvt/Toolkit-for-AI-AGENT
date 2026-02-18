---
name: Testing Tools
description: Testing frameworks - Jest, Vitest, Pytest, Cypress, Playwright, test strategies
---

# Testing Tools (Công cụ Testing)

## Tổng quan

Skill kit này hướng dẫn chi tiết về testing frameworks và tools: unit testing, integration testing, e2e testing, và test runner configuration.

---

## Checklist

- [ ] Chọn unit test framework (Jest / Vitest / Pytest)
- [ ] Chọn e2e test framework (Cypress / Playwright)
- [ ] Cấu hình test runner
- [ ] Setup coverage reporting
- [ ] Viết test cho critical paths
- [ ] Integrate tests vào CI/CD
- [ ] Setup test database

---

## Hướng dẫn chi tiết

### Chọn Framework

| Framework | Language | Tốt cho | Tốc độ |
|-----------|----------|---------|--------|
| **Vitest** | TypeScript | Vite projects, modern | ⚡ Rất nhanh |
| **Jest** | JS/TS | General purpose, phổ biến | 🔵 Nhanh |
| **Pytest** | Python | Python projects | ⚡ Rất nhanh |
| **Playwright** | Any | Cross-browser e2e | ⚡ Nhanh |
| **Cypress** | JS | Frontend e2e | 🔵 Trung bình |

---

### Vitest (Recommended cho TypeScript)

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    globals: true,
    environment: 'node',      // 'jsdom' cho frontend
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      thresholds: { lines: 80, branches: 80, functions: 80 },
    },
    setupFiles: ['./tests/setup.ts'],
  },
});

// ==========================================
// Unit Test Example
// ==========================================
// tests/services/user.service.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest';

describe('UserService', () => {
  let userService: UserService;
  let mockUserRepo: any;

  beforeEach(() => {
    mockUserRepo = {
      findByEmail: vi.fn(),
      create: vi.fn(),
    };
    userService = new UserService(mockUserRepo);
  });

  describe('register', () => {
    it('should create user with hashed password', async () => {
      mockUserRepo.findByEmail.mockResolvedValue(null);
      mockUserRepo.create.mockResolvedValue({ id: '1', email: 'test@test.com' });

      const result = await userService.register({
        email: 'test@test.com',
        password: 'password123',
        name: 'Test User',
      });

      expect(result).toBeDefined();
      expect(mockUserRepo.create).toHaveBeenCalledWith(
        expect.objectContaining({ email: 'test@test.com' })
      );
    });

    it('should throw if email already exists', async () => {
      mockUserRepo.findByEmail.mockResolvedValue({ id: '1' });

      await expect(
        userService.register({ email: 'existing@test.com', password: '123', name: 'Test' })
      ).rejects.toThrow('Email already exists');
    });
  });
});

// ==========================================
// Integration Test Example
// ==========================================
// tests/integration/auth.test.ts
import { describe, it, expect, beforeAll, afterAll } from 'vitest';
import supertest from 'supertest';
import app from '../../src/app';

const request = supertest(app);

describe('POST /api/v1/auth/register', () => {
  it('should register new user', async () => {
    const res = await request.post('/api/v1/auth/register').send({
      email: 'new@test.com',
      password: 'Password123!',
      name: 'New User',
    });

    expect(res.status).toBe(201);
    expect(res.body.success).toBe(true);
    expect(res.body.data.accessToken).toBeDefined();
  });

  it('should return 409 for duplicate email', async () => {
    // Register first
    await request.post('/api/v1/auth/register').send({
      email: 'dup@test.com', password: 'Password123!', name: 'User'
    });

    // Try again
    const res = await request.post('/api/v1/auth/register').send({
      email: 'dup@test.com', password: 'Password123!', name: 'User'
    });

    expect(res.status).toBe(409);
  });

  it('should return 400 for invalid email', async () => {
    const res = await request.post('/api/v1/auth/register').send({
      email: 'not-email', password: 'Password123!', name: 'User'
    });

    expect(res.status).toBe(400);
  });
});
```

---

### Pytest (Python)

```python
# conftest.py
import pytest
from fastapi.testclient import TestClient
from app.main import app
from app.core.database import get_db

@pytest.fixture
def client():
    return TestClient(app)

@pytest.fixture
def auth_headers(client):
    res = client.post("/api/v1/auth/register", json={
        "email": "test@test.com", "password": "Password123!", "name": "Test"
    })
    token = res.json()["data"]["access_token"]
    return {"Authorization": f"Bearer {token}"}

# ==========================================
# Unit Test
# ==========================================
# tests/test_user_service.py
from unittest.mock import MagicMock
from app.services.user_service import UserService

def test_register_creates_user():
    mock_repo = MagicMock()
    mock_repo.find_by_email.return_value = None
    mock_repo.create.return_value = {"id": "1", "email": "test@test.com"}

    service = UserService(mock_repo)
    result = service.register("test@test.com", "password123", "Test User")

    assert result is not None
    mock_repo.create.assert_called_once()

def test_register_raises_on_duplicate():
    mock_repo = MagicMock()
    mock_repo.find_by_email.return_value = {"id": "1"}

    service = UserService(mock_repo)
    with pytest.raises(ConflictError):
        service.register("existing@test.com", "password", "Test")

# ==========================================
# Integration Test
# ==========================================
# tests/test_auth_api.py
def test_register(client):
    res = client.post("/api/v1/auth/register", json={
        "email": "new@test.com", "password": "Password123!", "name": "User"
    })
    assert res.status_code == 201
    assert res.json()["success"] is True

def test_login(client):
    # Register first
    client.post("/api/v1/auth/register", json={
        "email": "login@test.com", "password": "Password123!", "name": "User"
    })
    # Login
    res = client.post("/api/v1/auth/login", json={
        "email": "login@test.com", "password": "Password123!"
    })
    assert res.status_code == 200
    assert "access_token" in res.json()["data"]

def test_protected_route_requires_auth(client):
    res = client.get("/api/v1/profile")
    assert res.status_code == 401
```

---

### Playwright (E2E Testing)

```typescript
// e2e/auth.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Authentication', () => {
  test('user can register and login', async ({ page }) => {
    // Go to register page
    await page.goto('/register');

    // Fill form
    await page.fill('[data-testid="email"]', 'e2e@test.com');
    await page.fill('[data-testid="password"]', 'Password123!');
    await page.fill('[data-testid="name"]', 'E2E User');
    await page.click('[data-testid="submit"]');

    // Should redirect to dashboard
    await expect(page).toHaveURL('/dashboard');
    await expect(page.locator('[data-testid="welcome"]')).toContainText('E2E User');
  });

  test('shows error for invalid credentials', async ({ page }) => {
    await page.goto('/login');
    await page.fill('[data-testid="email"]', 'wrong@test.com');
    await page.fill('[data-testid="password"]', 'wrong');
    await page.click('[data-testid="submit"]');

    await expect(page.locator('[data-testid="error"]')).toBeVisible();
  });
});

// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  use: {
    baseURL: 'http://localhost:3000',
    screenshot: 'only-on-failure',
    trace: 'on-first-retry',
  },
  webServer: {
    command: 'npm run dev',
    port: 3000,
    reuseExistingServer: !process.env.CI,
  },
});
```

---

### Test Commands

```bash
# Vitest
npm run test                  # Run all tests
npm run test -- --watch       # Watch mode
npm run test -- --coverage    # With coverage
npm run test -- auth          # Run tests matching "auth"

# Pytest
pytest                        # Run all tests
pytest -v                     # Verbose
pytest --cov=app              # With coverage
pytest tests/test_auth.py     # Specific file
pytest -k "test_login"        # Run tests matching name

# Playwright
npx playwright test           # Run all e2e tests
npx playwright test --ui      # Interactive UI mode
npx playwright show-report    # View HTML report
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Không có tests | Test critical paths ít nhất |
| Test implementation details | Test behavior / contract |
| Test quá chi tiết (brittle) | Test inputs → outputs |
| Dùng production DB cho tests | Separate test database |
| Không mock external services | Mock API calls, DB, etc. |
| E2E test cho mọi thứ | Unit > Integration > E2E (pyramid) |

---

## Liên kết

- **Process liên quan:** [06-Testing](../../processes/06-testing/SKILL.md)
- **Skill liên quan:** [ci-cd](../ci-cd/SKILL.md), [backend-development](../backend-development/SKILL.md)
