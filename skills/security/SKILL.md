---
name: Security
description: Bảo mật - OWASP Top 10, input validation, XSS/CSRF/SQL injection prevention
---

# Security (Bảo mật)

## Tổng quan

Skill kit này hướng dẫn bảo mật ứng dụng web: OWASP Top 10, input validation, attack prevention, và security headers.

---

## Checklist

- [ ] Input validation & sanitization
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (output encoding)
- [ ] CSRF protection
- [ ] Security headers (Helmet.js)
- [ ] HTTPS everywhere
- [ ] Password hashing (bcrypt, cost ≥ 12)
- [ ] Rate limiting
- [ ] CORS configuration
- [ ] Sensitive data encryption
- [ ] Dependency security audit (`npm audit`)
- [ ] Principle of least privilege

---

## Hướng dẫn chi tiết

### OWASP Top 10 — Quick Reference

| # | Vulnerability | Prevention |
|---|--------------|------------|
| 1 | **Injection** (SQL, NoSQL, LDAP) | Parameterized queries, ORM |
| 2 | **Broken Auth** | Strong passwords, MFA, rate limiting |
| 3 | **Sensitive Data Exposure** | HTTPS, encrypt at rest, minimize data |
| 4 | **XXE** | Disable XML external entities |
| 5 | **Broken Access Control** | RBAC, validate ownership |
| 6 | **Security Misconfig** | Disable defaults, security headers |
| 7 | **XSS** | Output encoding, CSP headers |
| 8 | **Insecure Deserialization** | Validate input, use safe formats |
| 9 | **Using Components with Vulnerabilities** | `npm audit`, update deps |
| 10 | **Insufficient Logging** | Log security events, monitor |

### SQL Injection Prevention

```javascript
// ❌ VULNERABLE
const query = `SELECT * FROM users WHERE email = '${email}'`;

// ✅ SAFE — Parameterized query
const query = 'SELECT * FROM users WHERE email = $1';
const result = await db.query(query, [email]);

// ✅ SAFE — ORM (Knex.js)
const user = await knex('users').where({ email }).first();
```

### XSS Prevention

```javascript
// ❌ VULNERABLE — React dangerouslySetInnerHTML
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// ✅ SAFE — React auto-escapes
<div>{userInput}</div>

// ✅ Server-side sanitization (DOMPurify)
import DOMPurify from 'isomorphic-dompurify';
const clean = DOMPurify.sanitize(userInput);
```

### Security Headers (Helmet.js)

```javascript
import helmet from 'helmet';
app.use(helmet());
// Sets: X-Content-Type-Options, X-Frame-Options, X-XSS-Protection,
//       Strict-Transport-Security, Content-Security-Policy, etc.

// Custom CSP
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'", "'unsafe-inline'"],
    styleSrc: ["'self'", "'unsafe-inline'"],
    imgSrc: ["'self'", 'data:', 'https:'],
  },
}));
```

### Rate Limiting

```javascript
import rateLimit from 'express-rate-limit';

// General API rate limit
const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100,
  message: { success: false, error: { code: 'RATE_LIMIT', message: 'Too many requests' } }
});

// Strict limit for auth endpoints
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  message: { success: false, error: { code: 'RATE_LIMIT', message: 'Too many attempts' } }
});

app.use('/api/', apiLimiter);
app.use('/api/auth/login', authLimiter);
```

### Input Validation

```typescript
// Validate AND sanitize input
import { z } from 'zod';

const userSchema = z.object({
  email: z.string().email().toLowerCase().trim(),
  name: z.string().min(2).max(100).trim(),
  password: z.string().min(8).max(128),
  age: z.number().int().min(0).max(150).optional(),
});

// Never trust client input — validate everything
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| String concatenation trong SQL | Parameterized queries |
| Trust client input | Validate mọi input ở server |
| Store passwords plain text | bcrypt with cost ≥ 12 |
| CORS `origin: '*'` | Specify allowed origins |
| API keys trong frontend code | Server-side proxy |
| No rate limiting | Rate limit all endpoints |

---

## Liên kết

- **Process liên quan:** [07-Code Review](../../processes/07-code-review/SKILL.md)
- **Skill liên quan:** [authentication](../authentication/SKILL.md), [error-handling](../error-handling/SKILL.md)
