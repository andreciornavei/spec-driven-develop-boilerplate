# Bug Fix Workflow

## Overview

This workflow demonstrates how to use agents to identify, fix, and prevent bugs.

## Scenario: Fix Payment Processing Bug

**Issue:** Users report failed payment processing with unclear error messages
**Severity:** High (Critical business functionality)
**Estimated Impact:** ~5% of transactions failing
**Priority:** P0

## Workflow Steps

### Step 1: Issue Analysis

**Agent:** Testing Agent + Backend Architecture Agent

**Task:** Reproduce and analyze the bug

**Steps:**
1. Review error logs
2. Identify patterns in failures
3. Reproduce the issue
4. Analyze root cause

**Input:**
```
Analyze payment processing failures:
- Error logs show "Payment validation failed" 
- Happens ~5% of the time
- No clear pattern in user types
- Stripe webhooks sometimes delayed
- State inconsistency reported
```

**Analysis Output:**
```markdown
## Root Cause Analysis

### Symptoms
- 5% of payments fail with generic error
- State inconsistency between app and Stripe
- Webhooks delayed by 2-5 seconds

### Investigation Steps
1. Reviewed error logs - found race condition
2. Analyzed Stripe webhook timing
3. Checked database transactions

### Root Cause
Race condition between:
1. User payment submission
2. Stripe webhook arrival
3. Order state update

When webhook arrives before order is fully created,
state machine rejects the update.

### Impact
- Failed payments
- Orphaned Stripe charges
- Customer frustration
- Revenue loss: ~$X/month
```

---

### Step 2: Design Fix

**Agent:** Backend Architecture Agent

**Task:** Design solution to prevent race condition

**Solution Design:**
```markdown
## Solution Architecture

### Approach
Implement idempotent payment processing with state locking

### Changes Required
1. Add payment intent ID to order before Stripe call
2. Implement optimistic locking on order updates
3. Queue webhook processing
4. Add idempotency keys
5. Improve error handling and retry logic

### Data Flow (Fixed)
1. Create order with 'payment_pending' status
2. Generate idempotency key
3. Store payment intent ID before Stripe call
4. Call Stripe with idempotency key
5. Queue webhook for processing
6. Process webhook with lock on order
7. Update order status atomically
```

**Deliverable:** `docs/fixes/payment-race-condition.md`

---

### Step 3: Implement Fix

**Agent:** Business Logic Agent + API Development Agent

**Implementation:**

```typescript
// Before (Buggy Code)
async function processPayment(orderId: string, paymentMethod: string) {
  const order = await Order.findById(orderId);
  
  // Race condition: webhook might arrive here
  const paymentIntent = await stripe.paymentIntents.create({
    amount: order.total * 100,
    currency: 'usd',
    payment_method: paymentMethod
  });
  
  // Update might fail if webhook already processed
  await order.update({
    paymentIntentId: paymentIntent.id,
    status: 'processing'
  });
}

// After (Fixed Code)
async function processPayment(orderId: string, paymentMethod: string) {
  // Generate idempotency key
  const idempotencyKey = `order_${orderId}_${Date.now()}`;
  
  // Update order first with lock
  const order = await Order.findById(orderId);
  if (order.status !== 'payment_pending') {
    throw new Error('Order not in valid state for payment');
  }
  
  // Store idempotency key
  await order.update({
    paymentIdempotencyKey: idempotencyKey,
    status: 'payment_processing'
  });
  
  try {
    // Create payment with idempotency
    const paymentIntent = await stripe.paymentIntents.create({
      amount: order.total * 100,
      currency: 'usd',
      payment_method: paymentMethod,
      metadata: {
        orderId: order.id,
        idempotencyKey
      }
    }, {
      idempotencyKey // Stripe idempotency
    });
    
    // Update with payment intent ID atomically
    await Order.update(
      {
        paymentIntentId: paymentIntent.id,
        status: 'payment_submitted'
      },
      {
        where: { 
          id: orderId,
          status: 'payment_processing' // Optimistic lock
        }
      }
    );
    
    return paymentIntent;
  } catch (error) {
    // Rollback on failure
    await order.update({ status: 'payment_failed' });
    throw error;
  }
}

// Webhook Handler (Fixed)
async function handlePaymentWebhook(event: StripeEvent) {
  const paymentIntent = event.data.object as PaymentIntent;
  const orderId = paymentIntent.metadata.orderId;
  
  // Queue webhook for processing to prevent timeout
  await webhookQueue.add('process-payment-webhook', {
    eventId: event.id,
    orderId,
    status: paymentIntent.status
  }, {
    attempts: 3,
    backoff: { type: 'exponential', delay: 2000 }
  });
  
  // Return 200 immediately to Stripe
  return { received: true };
}

// Webhook Queue Processor
async function processPaymentWebhook(job: Job) {
  const { orderId, status } = job.data;
  
  // Use transaction with lock
  await sequelize.transaction(async (t) => {
    const order = await Order.findById(orderId, {
      lock: t.LOCK.UPDATE, // Row-level lock
      transaction: t
    });
    
    // Verify state is valid for update
    if (order.status === 'payment_complete') {
      // Webhook already processed (idempotent)
      return;
    }
    
    // Update order status based on webhook
    await order.update({
      status: status === 'succeeded' ? 'payment_complete' : 'payment_failed',
      paymentCompletedAt: new Date()
    }, { transaction: t });
    
    // Trigger next steps
    if (status === 'succeeded') {
      await fulfillmentQueue.add('fulfill-order', { orderId });
    }
  });
}
```

**Files Changed:**
- `src/services/paymentService.ts`
- `src/handlers/stripeWebhook.ts`
- `src/queues/webhookQueue.ts`
- `migrations/20260208_add_payment_idempotency.sql`

---

### Step 4: Add Tests

**Agent:** Testing Agent

**Task:** Create tests to prevent regression

**Test Cases:**
```typescript
describe('Payment Processing', () => {
  describe('Race Condition Prevention', () => {
    it('handles webhook arriving before order update', async () => {
      const order = await createTestOrder();
      
      // Start payment processing
      const paymentPromise = paymentService.processPayment(
        order.id, 
        'pm_test'
      );
      
      // Simulate webhook arriving immediately
      await delay(10);
      await webhookHandler.handle({
        type: 'payment_intent.succeeded',
        data: { object: { id: 'pi_test', metadata: { orderId: order.id } } }
      });
      
      // Wait for payment to complete
      await paymentPromise;
      
      // Verify final state is correct
      const finalOrder = await Order.findById(order.id);
      expect(finalOrder.status).toBe('payment_complete');
      expect(finalOrder.paymentIntentId).toBe('pi_test');
    });
    
    it('is idempotent when webhook received multiple times', async () => {
      const order = await createPaymentProcessedOrder();
      
      // Send webhook twice
      await webhookHandler.handle(createWebhookEvent(order));
      await webhookHandler.handle(createWebhookEvent(order));
      
      // Verify order only updated once
      const logs = await PaymentLog.findAll({ where: { orderId: order.id } });
      expect(logs.length).toBe(1);
    });
    
    it('handles concurrent payment attempts', async () => {
      const order = await createTestOrder();
      
      // Try to process payment twice concurrently
      const results = await Promise.allSettled([
        paymentService.processPayment(order.id, 'pm_test_1'),
        paymentService.processPayment(order.id, 'pm_test_2')
      ]);
      
      // One should succeed, one should fail
      const succeeded = results.filter(r => r.status === 'fulfilled');
      const failed = results.filter(r => r.status === 'rejected');
      
      expect(succeeded.length).toBe(1);
      expect(failed.length).toBe(1);
    });
  });
  
  describe('Error Recovery', () => {
    it('recovers from Stripe API failure', async () => {
      const order = await createTestOrder();
      
      // Mock Stripe failure
      stripe.paymentIntents.create.mockRejectedValueOnce(
        new Error('Stripe API error')
      );
      
      await expect(
        paymentService.processPayment(order.id, 'pm_test')
      ).rejects.toThrow('Stripe API error');
      
      // Verify order status rolled back
      const failedOrder = await Order.findById(order.id);
      expect(failedOrder.status).toBe('payment_failed');
    });
  });
});
```

**Coverage Results:**
- Payment service: 95%
- Webhook handler: 92%
- Edge cases: 88%

---

### Step 5: Security Review

**Agent:** Security Agent

**Task:** Verify fix doesn't introduce security issues

**Security Checks:**
- ✅ Webhook signature validation
- ✅ Idempotency key generation secure
- ✅ No sensitive data in logs
- ✅ Proper error messages (no info leakage)
- ✅ Authorization checks intact
- ✅ SQL injection prevented (using ORM)
- ✅ Rate limiting on payment endpoints

**Additional Recommendations:**
- Add monitoring for failed payment attempts
- Alert on repeated failures from same IP
- Log all payment state transitions

---

### Step 6: Performance Testing

**Agent:** Performance Optimization Agent

**Task:** Verify fix doesn't degrade performance

**Load Test Results:**
```
Scenario: 1000 concurrent payments

Before Fix:
- Success rate: 95%
- Avg response time: 450ms
- P95 response time: 850ms
- Race conditions: ~50 (5%)

After Fix:
- Success rate: 99.9%
- Avg response time: 480ms (+30ms)
- P95 response time: 920ms (+70ms)
- Race conditions: 0

Conclusion: Slight performance decrease acceptable 
for reliability improvement
```

---

### Step 7: Documentation

**Agent:** Documentation Agent

**Updates:**
1. Payment processing flow diagram
2. Webhook handling documentation
3. Error recovery procedures
4. Monitoring and alerting guide

**New Documentation:**
```markdown
## Payment Processing

### Overview
The payment system uses Stripe for processing with 
race condition prevention through:
- Idempotency keys
- Optimistic locking
- Webhook queuing
- State machine validation

### Error Handling
- Stripe API failures → order status: payment_failed
- Webhook timeouts → automatic retry (3 attempts)
- Invalid state transitions → rejected with error log

### Monitoring
- Alert on payment success rate < 95%
- Alert on webhook processing delays > 10s
- Dashboard: payment-processing-overview
```

---

### Step 8: Deployment Plan

**Agent:** DevOps & Deployment Agent

**Deployment Strategy:**

```yaml
# Deployment Plan

## Pre-Deployment
- [x] Run all tests
- [x] Database migration ready
- [x] Feature flag configured: payment_v2
- [x] Rollback plan documented
- [x] Monitoring dashboard ready
- [x] Team notification sent

## Deployment Steps
1. Deploy to staging
2. Run smoke tests
3. Enable feature flag for 10% traffic
4. Monitor for 30 minutes
5. Increase to 50% traffic
6. Monitor for 1 hour
7. Enable for 100% traffic

## Monitoring
- Watch payment success rate
- Monitor webhook processing queue
- Track error rates
- Check database lock contention

## Rollback Triggers
- Payment success rate drops below 90%
- Webhook queue length > 1000
- Database lock timeout errors
- Critical errors in logs

## Rollback Plan
1. Disable feature flag
2. Monitor recovery
3. Investigate issues
4. Fix and redeploy
```

---

### Step 9: Post-Deployment Monitoring

**Monitoring Results (24 hours):**
```
✅ Payment success rate: 99.8% (was 95%)
✅ Webhook processing: avg 45ms
✅ No race conditions detected
✅ Customer complaints: dropped by 90%
✅ Revenue impact: recovered ~$X/day
```

---

### Step 10: Retrospective

**Agent:** Documentation Agent

**Learnings:**
```markdown
## Post-Mortem: Payment Race Condition

### What Went Well
- Quick identification of root cause
- Comprehensive test coverage added
- Gradual rollout prevented issues
- Good team collaboration

### What Could Improve
- Earlier detection through monitoring
- More thorough initial testing
- Better race condition testing in CI

### Action Items
1. Add race condition detection tests to CI
2. Implement payment monitoring earlier
3. Document concurrency patterns
4. Add performance testing to pre-deploy
5. Create race condition testing guide

### Prevention
- Added webhook latency monitoring
- Created payment state machine tests
- Documented concurrency patterns
- Added load testing to CI/CD
```

---

## Summary

**Timeline:**
- Day 1: Issue identified and analyzed
- Day 2: Solution designed and reviewed
- Day 3-4: Implementation and testing
- Day 5: Deployment and monitoring

**Impact:**
- Bug fixed: ✅
- Success rate improved: 95% → 99.8%
- Revenue recovered: ~$X/month
- Customer satisfaction: Improved
- Technical debt: Reduced

**Artifacts:**
- Code changes: 8 files
- Tests added: 15 test cases
- Documentation: 4 documents
- Monitoring: 3 new dashboards

---

*This workflow demonstrates systematic bug fixing using the agent-driven approach.*
