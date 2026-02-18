---
name: Payment Integration
description: Payment - Stripe, PayPal, VNPay, webhook handling, subscription billing
---

# Payment Integration (Thanh toán)

## Tổng quan

Skill kit này hướng dẫn tích hợp thanh toán: Stripe, PayPal, VNPay, webhook handling, và subscription billing.

---

## Checklist

- [ ] Chọn payment provider (Stripe / PayPal / VNPay...)
- [ ] Setup API keys (test + production)
- [ ] Implement checkout flow
- [ ] Setup webhook endpoint
- [ ] Verify webhook signatures
- [ ] Handle payment states (pending, success, failed)
- [ ] Store transaction records
- [ ] Implement refund flow
- [ ] Error handling cho payment failures
- [ ] Test với sandbox/test mode

---

## Hướng dẫn chi tiết

### Payment Provider Comparison

| Provider | Tốt cho | Phí | VN Support |
|----------|---------|-----|------------|
| **Stripe** | Global, modern API | 2.9% + 30¢ | Limited |
| **PayPal** | Global, user trust | 2.9% + 30¢ | Yes |
| **VNPay** | Vietnam market | Thỏa thuận | ✅ Native |
| **Momo** | Vietnam mobile | Thỏa thuận | ✅ Native |

### Stripe Integration

```typescript
// Backend — Stripe Checkout Session
import Stripe from 'stripe';

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);

// Create checkout session
router.post('/api/checkout', authenticate, async (req, res) => {
  const { items } = req.body;

  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    line_items: items.map(item => ({
      price_data: {
        currency: 'usd',
        product_data: { name: item.name },
        unit_amount: Math.round(item.price * 100), // cents
      },
      quantity: item.quantity,
    })),
    mode: 'payment',
    success_url: `${process.env.CLIENT_URL}/checkout/success?session_id={CHECKOUT_SESSION_ID}`,
    cancel_url: `${process.env.CLIENT_URL}/checkout/cancel`,
    metadata: { userId: req.user.id },
  });

  res.json({ success: true, data: { sessionUrl: session.url } });
});
```

### Webhook Handling

```typescript
// CRITICAL: Verify webhook signature to prevent fraud
router.post('/api/webhooks/stripe',
  express.raw({ type: 'application/json' }), // Raw body required
  async (req, res) => {
    const sig = req.headers['stripe-signature']!;

    let event: Stripe.Event;
    try {
      event = stripe.webhooks.constructEvent(
        req.body,
        sig,
        process.env.STRIPE_WEBHOOK_SECRET!
      );
    } catch (err) {
      logger.error('Webhook signature verification failed', { error: err.message });
      return res.status(400).send('Invalid signature');
    }

    // Handle events
    switch (event.type) {
      case 'checkout.session.completed':
        const session = event.data.object;
        await handlePaymentSuccess(session);
        break;

      case 'payment_intent.payment_failed':
        const intent = event.data.object;
        await handlePaymentFailed(intent);
        break;

      default:
        logger.info(`Unhandled event type: ${event.type}`);
    }

    res.json({ received: true });
  }
);

async function handlePaymentSuccess(session: Stripe.Checkout.Session) {
  const order = await orderRepo.create({
    userId: session.metadata.userId,
    stripeSessionId: session.id,
    amount: session.amount_total / 100,
    status: 'paid',
  });

  await emailService.sendOrderConfirmation(order);
  logger.info('Payment successful', { orderId: order.id });
}
```

### Transaction Record Schema

```sql
CREATE TABLE transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    order_id UUID REFERENCES orders(id),
    provider VARCHAR(50) NOT NULL,         -- 'stripe', 'paypal', 'vnpay'
    provider_transaction_id VARCHAR(255),   -- ID from payment provider
    amount DECIMAL(10,2) NOT NULL,
    currency VARCHAR(3) DEFAULT 'USD',
    status VARCHAR(50) DEFAULT 'pending',   -- pending, success, failed, refunded
    metadata JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Trust client-side amount | Tính toán amount ở server |
| Không verify webhook signature | Luôn verify signature |
| Test với production keys | Dùng test/sandbox mode |
| Không lưu transaction records | Log mọi transaction |
| Không handle failed payments | Retry logic + notify user |
| Float cho tiền | Integer (cents) hoặc DECIMAL |

---

## Liên kết

- **Skill liên quan:** [backend-development](../backend-development/SKILL.md), [security](../security/SKILL.md), [error-handling](../error-handling/SKILL.md)
