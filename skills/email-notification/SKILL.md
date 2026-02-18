---
name: Email & Notification
description: Notifications - email (SMTP/SendGrid), push notification, SMS, in-app notification
---

# Email & Notification

## Tổng quan

Skill kit này hướng dẫn implement hệ thống thông báo: email, push notification, SMS, và in-app notifications.

---

## Checklist

- [ ] Chọn email service (SMTP / SendGrid / AWS SES)
- [ ] Setup email service abstraction
- [ ] Tạo email templates (HTML)
- [ ] Implement notification types (email, in-app, push)
- [ ] Queue notifications (không block request)
- [ ] Handle failures + retry
- [ ] Unsubscribe mechanism

---

## Hướng dẫn chi tiết

### Email Service (Nodemailer)

```typescript
import nodemailer from 'nodemailer';

// Config
const transporter = nodemailer.createTransport({
  host: process.env.SMTP_HOST,
  port: +process.env.SMTP_PORT!,
  secure: false,
  auth: {
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASS,
  },
});

// Email Service
class EmailService {
  async sendEmail(to: string, subject: string, html: string) {
    try {
      await transporter.sendMail({
        from: `"${process.env.APP_NAME}" <${process.env.SMTP_FROM}>`,
        to,
        subject,
        html,
      });
      logger.info('Email sent', { to, subject });
    } catch (error) {
      logger.error('Email failed', { to, subject, error: error.message });
      throw error;
    }
  }

  async sendWelcome(user: User) {
    const html = welcomeTemplate({ name: user.name, appUrl: process.env.APP_URL });
    await this.sendEmail(user.email, `Welcome to ${process.env.APP_NAME}!`, html);
  }

  async sendPasswordReset(email: string, resetToken: string) {
    const resetUrl = `${process.env.APP_URL}/reset-password?token=${resetToken}`;
    const html = resetPasswordTemplate({ resetUrl });
    await this.sendEmail(email, 'Reset your password', html);
  }

  async sendOrderConfirmation(order: Order) {
    const html = orderConfirmationTemplate({ order });
    await this.sendEmail(order.user.email, `Order #${order.id} confirmed`, html);
  }
}
```

### Email Template

```typescript
// templates/welcome.ts
export const welcomeTemplate = ({ name, appUrl }: { name: string; appUrl: string }) => `
<!DOCTYPE html>
<html>
<head>
  <style>
    body { font-family: Arial, sans-serif; max-width: 600px; margin: 0 auto; }
    .header { background: #4F46E5; color: white; padding: 20px; text-align: center; }
    .content { padding: 20px; }
    .button {
      display: inline-block; padding: 12px 24px;
      background: #4F46E5; color: white; text-decoration: none;
      border-radius: 6px; margin: 20px 0;
    }
    .footer { color: #666; font-size: 12px; text-align: center; padding: 20px; }
  </style>
</head>
<body>
  <div class="header"><h1>Welcome!</h1></div>
  <div class="content">
    <p>Hi ${name},</p>
    <p>Thanks for joining us!</p>
    <a href="${appUrl}/dashboard" class="button">Get Started</a>
  </div>
  <div class="footer">
    <p>If you didn't create this account, please ignore this email.</p>
  </div>
</body>
</html>
`;
```

### In-App Notification System

```typescript
// Notification model
// notifications table: id, user_id, type, title, message, data (JSONB), read, created_at

class NotificationService {
  async create(userId: string, type: string, title: string, message: string, data?: any) {
    const notification = await notificationRepo.create({
      userId, type, title, message, data
    });

    // Real-time push via WebSocket
    io.to(`user:${userId}`).emit('notification', notification);

    return notification;
  }

  async markAsRead(notificationId: string, userId: string) {
    await notificationRepo.update(notificationId, { read: true });
  }

  async getUnread(userId: string) {
    return notificationRepo.findMany({ userId, read: false });
  }
}
```

### Notification Queue (Bull/BullMQ)

```typescript
import { Queue, Worker } from 'bullmq';

const emailQueue = new Queue('email', { connection: redisConnection });

// Producer — add to queue (non-blocking)
await emailQueue.add('welcome', { userId: user.id, email: user.email });
await emailQueue.add('order-confirm', { orderId: order.id });

// Worker — process queue
const worker = new Worker('email', async (job) => {
  switch (job.name) {
    case 'welcome':
      await emailService.sendWelcome(job.data);
      break;
    case 'order-confirm':
      await emailService.sendOrderConfirmation(job.data);
      break;
  }
}, {
  connection: redisConnection,
  attempts: 3,          // Retry 3 times
  backoff: { type: 'exponential', delay: 2000 },
});
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Gửi email synchronous (block request) | Queue emails, xử lý async |
| Không retry khi fail | Retry với exponential backoff |
| Hard-code email content | Dùng templates |
| Không có unsubscribe | Luôn có link unsubscribe |
| Test với production email | Dùng Mailtrap / Ethereal cho dev |
| Gửi quá nhiều notifications | Rate limit + user preferences |

---

## Liên kết

- **Skill liên quan:** [backend-development](../backend-development/SKILL.md), [real-time](../real-time/SKILL.md)
