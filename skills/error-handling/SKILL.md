---
name: Error Handling
description: Xử lý lỗi - error types, try-catch strategy, error logging, user-friendly messages
---

# Error Handling (Xử lý lỗi)

## Tổng quan

Skill kit này hướng dẫn chiến lược xử lý lỗi toàn diện: custom error classes, global error handler, logging, và user-friendly messages.

---

## Checklist

- [ ] Tạo custom error classes (AppError, NotFound, Validation...)
- [ ] Setup global error handler middleware
- [ ] Phân biệt operational vs programming errors
- [ ] Error logging với đủ context
- [ ] User-friendly error messages (không lộ internal details)
- [ ] Frontend error boundaries (React)
- [ ] Retry logic cho transient errors
- [ ] Graceful shutdown handling

---

## Hướng dẫn chi tiết

### Error Hierarchy

```typescript
// Base error class
class AppError extends Error {
  constructor(
    public statusCode: number,
    public code: string,
    message: string,
    public isOperational = true
  ) {
    super(message);
    Error.captureStackTrace(this, this.constructor);
  }
}

// Specific errors
class BadRequestError extends AppError {
  constructor(message = 'Bad request') {
    super(400, 'BAD_REQUEST', message);
  }
}

class NotFoundError extends AppError {
  constructor(resource = 'Resource') {
    super(404, 'NOT_FOUND', `${resource} not found`);
  }
}

class ConflictError extends AppError {
  constructor(message = 'Resource already exists') {
    super(409, 'CONFLICT', message);
  }
}

class InternalError extends AppError {
  constructor(message = 'Internal server error') {
    super(500, 'INTERNAL_ERROR', message, false); // not operational
  }
}
```

### Global Error Handler

```typescript
const errorHandler = (err: Error, req: Request, res: Response, next: NextFunction) => {
  // Default values
  let statusCode = 500;
  let code = 'INTERNAL_ERROR';
  let message = 'Something went wrong';

  if (err instanceof AppError) {
    statusCode = err.statusCode;
    code = err.code;
    message = err.message;
  }

  // Log error
  if (statusCode >= 500) {
    logger.error('Server error', { err, req: { method: req.method, url: req.url } });
  } else {
    logger.warn('Client error', { statusCode, code, message });
  }

  // NEVER expose internal details in production
  const response = {
    success: false,
    error: {
      code,
      message: statusCode >= 500 && process.env.NODE_ENV === 'production'
        ? 'Internal server error'
        : message,
    },
  };

  res.status(statusCode).json(response);
};
```

### Frontend Error Handling

```tsx
// React Error Boundary
class ErrorBoundary extends React.Component {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error boundary caught:', error, errorInfo);
    // Send to error tracking service
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback error={this.state.error} />;
    }
    return this.props.children;
  }
}

// API Error handling
const handleApiError = (error: any): string => {
  if (error.response) {
    // Server responded with error
    return error.response.data?.error?.message || 'Server error';
  } else if (error.request) {
    // No response received
    return 'Network error. Please check your connection.';
  } else {
    return 'An unexpected error occurred.';
  }
};
```

---

## Python / FastAPI

### Custom Exceptions

```python
# core/exceptions.py
class AppException(Exception):
    def __init__(self, status_code: int, code: str, message: str):
        self.status_code = status_code
        self.code = code
        self.message = message

class NotFoundError(AppException):
    def __init__(self, resource: str = "Resource"):
        super().__init__(404, "NOT_FOUND", f"{resource} not found")

class BadRequestError(AppException):
    def __init__(self, message: str = "Bad request"):
        super().__init__(400, "BAD_REQUEST", message)

class ConflictError(AppException):
    def __init__(self, message: str = "Resource already exists"):
        super().__init__(409, "CONFLICT", message)
```

### Global Exception Handler

```python
# main.py
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse
from fastapi.exceptions import RequestValidationError
import logging

logger = logging.getLogger(__name__)

@app.exception_handler(AppException)
async def app_exception_handler(request: Request, exc: AppException):
    if exc.status_code >= 500:
        logger.error(f"Server error: {exc.message}", exc_info=True)
    return JSONResponse(
        status_code=exc.status_code,
        content={"success": False, "error": {"code": exc.code, "message": exc.message}},
    )

@app.exception_handler(RequestValidationError)
async def validation_exception_handler(request: Request, exc: RequestValidationError):
    details = [{"field": ".".join(map(str, e["loc"])), "message": e["msg"]} for e in exc.errors()]
    return JSONResponse(
        status_code=422,
        content={"success": False, "error": {"code": "VALIDATION_ERROR", "message": "Invalid input", "details": details}},
    )

# Usage in route
@router.get("/products/{id}")
async def get_product(id: str, db: Session = Depends(get_db)):
    product = db.query(Product).filter(Product.id == id).first()
    if not product:
        raise NotFoundError("Product")
    return {"success": True, "data": product}
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| `catch (e) {}` — empty catch | Log error hoặc re-throw |
| Stack trace cho client | Chỉ expose user-friendly message |
| Dùng generic Error | Custom error classes with codes |
| `console.log(error)` | Proper logging library (Winston, Pino) |
| Không phân biệt error types | Operational vs Programming errors |

---

## Liên kết

- **Process liên quan:** [05-Implementation](../../processes/05-implementation/SKILL.md)
- **Skill liên quan:** [backend-development](../backend-development/SKILL.md), [logging-monitoring](../logging-monitoring/SKILL.md)
