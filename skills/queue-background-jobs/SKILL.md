---
name: Queue & Background Jobs
description: Xử lý bất đồng bộ - message queues, job scheduling, Bull, Celery, retry strategies
---

# Queue & Background Jobs

## Tổng quan

Skill kit này hướng dẫn xử lý tác vụ bất đồng bộ: message queues, background jobs, scheduled tasks. Giúp app không bị block bởi tác vụ nặng (gửi email, xử lý ảnh, tạo report...).

**Khi nào sử dụng:** Khi có tác vụ tốn thời gian hoặc cần chạy theo lịch.

---

## Checklist

- [ ] Xác định tác vụ nào cần chạy background
- [ ] Chọn queue library (Bull, BullMQ, Celery, RabbitMQ...)
- [ ] Setup Redis/RabbitMQ cho queue storage
- [ ] Implement job producers (tạo jobs)
- [ ] Implement job consumers/workers (xử lý jobs)
- [ ] Implement retry strategy (exponential backoff)
- [ ] Implement dead letter queue (DLQ) cho failed jobs
- [ ] Implement job monitoring / dashboard
- [ ] Implement scheduled/cron jobs (nếu cần)
- [ ] Handle graceful shutdown cho workers

---

## Khi nào cần Queue?

| Scenario | Queue? | Lý do |
|----------|--------|-------|
| Gửi email sau khi register | ✅ Có | Đừng để user chờ SMTP response |
| Resize/optimize ảnh upload | ✅ Có | Xử lý ảnh mất 2-10s |
| Tạo report PDF | ✅ Có | Tốn CPU, có thể mất phút |
| Sync data với third-party | ✅ Có | External API có thể chậm/fail |
| Scheduled cleanup (xóa temp files) | ✅ Có | Cron job chạy định kỳ |
| Đọc 1 record từ DB | ❌ Không | Nhanh, không cần async |
| Validate form input | ❌ Không | Phải trả kết quả ngay |

---

## Hướng dẫn chi tiết

### Node.js — BullMQ (Recommended)

```typescript
// 1. Setup Queue
// config/queue.ts
import { Queue, Worker, QueueScheduler } from 'bullmq';
import Redis from 'ioredis';

const connection = new Redis({
  host: process.env.REDIS_HOST || 'localhost',
  port: Number(process.env.REDIS_PORT) || 6379,
  maxRetriesPerRequest: null,
});

export const emailQueue = new Queue('email', { connection });
export const reportQueue = new Queue('report', { connection });
```

```typescript
// 2. Producer — Thêm job vào queue
// services/emailService.ts
import { emailQueue } from '../config/queue';

export async function sendWelcomeEmail(userId: string, email: string) {
  await emailQueue.add('welcome-email', 
    { userId, email, template: 'welcome' },
    {
      attempts: 3,                    // Retry 3 lần
      backoff: { type: 'exponential', delay: 2000 }, // 2s → 4s → 8s
      removeOnComplete: 100,          // Giữ 100 completed jobs
      removeOnFail: 500,              // Giữ 500 failed jobs
    }
  );
}

// Scheduled job — Chạy mỗi ngày lúc 2:00 AM
await reportQueue.add('daily-report', {}, {
  repeat: { cron: '0 2 * * *' },
});
```

```typescript
// 3. Worker — Xử lý jobs
// workers/emailWorker.ts
import { Worker, Job } from 'bullmq';

const emailWorker = new Worker('email', async (job: Job) => {
  console.log(`Processing job ${job.id}: ${job.name}`);
  
  switch (job.name) {
    case 'welcome-email':
      await sendEmail(job.data.email, job.data.template);
      break;
    case 'password-reset':
      await sendEmail(job.data.email, 'reset');
      break;
  }
}, {
  connection,
  concurrency: 5,     // Xử lý 5 jobs song song
  limiter: {
    max: 10,           // Max 10 jobs
    duration: 1000,    // mỗi 1 giây (rate limit)
  },
});

// Event handlers
emailWorker.on('completed', (job) => {
  console.log(`✅ Job ${job.id} completed`);
});

emailWorker.on('failed', (job, err) => {
  console.error(`❌ Job ${job?.id} failed: ${err.message}`);
});
```

### Python — Celery

```python
# 1. Setup Celery
# config/celery.py
from celery import Celery
from celery.schedules import crontab

app = Celery('myapp',
    broker='redis://localhost:6379/0',
    backend='redis://localhost:6379/1',
)

app.conf.update(
    task_serializer='json',
    result_serializer='json',
    accept_content=['json'],
    timezone='UTC',
    task_acks_late=True,          # Ack sau khi xử lý xong
    worker_prefetch_multiplier=1,  # 1 task/worker tại 1 thời điểm
)

# Scheduled tasks
app.conf.beat_schedule = {
    'daily-cleanup': {
        'task': 'tasks.cleanup_temp_files',
        'schedule': crontab(hour=2, minute=0),  # 2:00 AM daily
    },
}
```

```python
# 2. Define tasks
# tasks/email_tasks.py
from config.celery import app
from celery import shared_task
from celery.utils.log import get_task_logger

logger = get_task_logger(__name__)

@shared_task(
    bind=True,
    max_retries=3,
    default_retry_delay=60,  # 60s giữa các retry
    autoretry_for=(ConnectionError, TimeoutError),
)
def send_welcome_email(self, user_id: str, email: str):
    try:
        logger.info(f"Sending welcome email to {email}")
        # ... send email logic
        return {"status": "sent", "email": email}
    except Exception as exc:
        logger.error(f"Failed to send email: {exc}")
        raise self.retry(exc=exc)


# 3. Call task (Producer)
# Trong API handler:
send_welcome_email.delay(user.id, user.email)

# Hoặc với options:
send_welcome_email.apply_async(
    args=[user.id, user.email],
    countdown=30,           # Delay 30s trước khi chạy
    expires=3600,           # Hết hạn sau 1 giờ
)
```

### Retry Strategy

```
Attempt 1: Chạy ngay          → Fail
Attempt 2: Chờ 2s  rồi retry  → Fail  
Attempt 3: Chờ 4s  rồi retry  → Fail
Attempt 4: Chờ 8s  rồi retry  → Fail
→ Chuyển vào Dead Letter Queue (DLQ)
→ Alert admin / log error
```

### Project Structure

```
src/
├── queues/               # Queue definitions
│   ├── emailQueue.ts
│   └── reportQueue.ts
├── workers/              # Job processors
│   ├── emailWorker.ts
│   └── reportWorker.ts
├── jobs/                 # Job type definitions
│   ├── emailJobs.ts
│   └── reportJobs.ts
└── scripts/
    └── startWorker.ts    # Worker entry point
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Không có retry logic | Luôn có retry + exponential backoff |
| Failed jobs biến mất | Dùng Dead Letter Queue, log failures |
| Worker crash = mất job | Dùng `acks_late` / persistent queue |
| Không monitor queue | Setup dashboard (Bull Board, Flower) |
| Quá nhiều concurrent workers | Rate limit theo khả năng hệ thống |
| Không graceful shutdown | Handle SIGTERM, finish current job |

---

## Liên kết

- **Process liên quan:** [05-Implementation](../../processes/05-implementation/SKILL.md)
- **Skill liên quan:** [email-notification](../email-notification/SKILL.md), [logging-monitoring](../logging-monitoring/SKILL.md), [caching](../caching/SKILL.md)
