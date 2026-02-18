---
name: Logging & Monitoring
description: Logging & Monitoring - log levels, structured logging, health checks, alerting
---

# Logging & Monitoring

## Tổng quan

Skill kit này hướng dẫn setup logging và monitoring cho ứng dụng: structured logging, log levels, health checks, và alerting.

---

## Checklist

- [ ] Chọn logging library (Winston, Pino, Bunyan...)
- [ ] Setup log levels (error, warn, info, debug)
- [ ] Implement structured logging (JSON format)
- [ ] Setup request/response logging
- [ ] Implement health check endpoint
- [ ] Setup error tracking (Sentry, DataDog...)
- [ ] Setup alerting cho critical errors

---

## Hướng dẫn chi tiết

### Log Levels

```
ERROR  ← Production issues, needs immediate action
WARN   ← Potential problems, not critical
INFO   ← Important business events
DEBUG  ← Development/troubleshooting details

Production: ERROR, WARN, INFO
Development: All levels
```

### Structured Logging (Winston)

```typescript
import winston from 'winston';

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  transports: [
    new winston.transports.Console({
      format: process.env.NODE_ENV === 'development'
        ? winston.format.combine(winston.format.colorize(), winston.format.simple())
        : winston.format.json()
    }),
    new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
    new winston.transports.File({ filename: 'logs/combined.log' }),
  ],
});

// Usage
logger.info('User registered', { userId: user.id, email: user.email });
logger.error('Payment failed', { orderId, error: err.message, stack: err.stack });
logger.warn('Rate limit approaching', { ip: req.ip, count: requestCount });
```

### Health Check Endpoint

```typescript
app.get('/health', async (req, res) => {
  const health = {
    status: 'ok',
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
    checks: {
      database: await checkDatabase(),
      redis: await checkRedis(),
    }
  };

  const isHealthy = Object.values(health.checks).every(c => c === 'ok');
  res.status(isHealthy ? 200 : 503).json(health);
});
```

### Request Logging Middleware

```typescript
const requestLogger = (req, res, next) => {
  const start = Date.now();

  res.on('finish', () => {
    const duration = Date.now() - start;
    logger.info('HTTP Request', {
      method: req.method,
      url: req.url,
      status: res.statusCode,
      duration: `${duration}ms`,
      ip: req.ip,
      userAgent: req.headers['user-agent'],
    });
  });

  next();
};
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| `console.log` trong production | Dùng logging library |
| Log sensitive data (passwords) | Filter sensitive fields |
| No health check | `/health` endpoint bắt buộc |
| Log quá nhiều (debug in prod) | Đúng level cho đúng environment |
| Không log errors đầy đủ | Log error message + stack + context |

---

## Liên kết

- **Process liên quan:** [08-Deployment](../../processes/08-deployment/SKILL.md), [10-Maintenance](../../processes/10-maintenance/SKILL.md)
- **Skill liên quan:** [error-handling](../error-handling/SKILL.md)
