# 支付上下文 - 领域模型设计

**文档状态**：NEW

## 1. 上下文概述

- **限界上下文名称**：支付
- **主要职责**：处理订单支付，包括创建支付、支付结果回调、退款处理
- **边界范围**：支付记录管理，支付结果处理，退款处理

## 2. 聚合设计

### 2.1 支付记录 \[NEW]

- **聚合根**：Payment
- **不变性约束**：支付金额必须等于订单金额，支付状态流转需合法
- **包含实体/值对象**：PaymentMethod（复用自订单履约上下文）

## 3. 领域模型详细设计

### 3.1 实体

#### 支付记录 \[NEW]

```java
class Payment {
    private PaymentId id;
    private OrderId orderId;
    private BigDecimal amount;
    private PaymentMethod paymentMethod;
    private PaymentStatus status;
    private LocalDateTime createdAt;
    private LocalDateTime succeededAt;
    private LocalDateTime failedAt;
    private String failureReason;
    
    // 支付成功
    void succeed(String transactionId) {
        assertStatus(PaymentStatus.PENDING);
        this.status = PaymentStatus.SUCCEEDED;
        this.succeededAt = now();
        publishEvent(PaymentSucceededEvent.of(this.id, this.orderId, transactionId));
    }
    
    // 支付失败
    void fail(String reason) {
        assertStatus(PaymentStatus.PENDING);
        this.status = PaymentStatus.FAILED;
        this.failedAt = now();
        this.failureReason = reason;
        publishEvent(PaymentFailedEvent.of(this.id, this.orderId, reason));
    }
    
    // 退款
    void refund(String reason) {
        assertStatus(PaymentStatus.SUCCEEDED);
        this.status = PaymentStatus.REFUNDED;
        publishEvent(PaymentRefundedEvent.of(this.id, this.orderId, reason));
    }
}
```

- **唯一标识**：PaymentId
- **关键行为**：创建、支付成功、支付失败、退款

### 3.2 值对象

#### 支付ID \[NEW]

```java
class PaymentId {
    private final String value;
}
```

#### 支付状态 \[NEW]

```java
enum PaymentStatus {
    PENDING,    // 待支付
    SUCCEEDED,  // 支付成功
    FAILED,     // 支付失败
    REFUNDED    // 已退款
}
```

#### 支付方式 \[NEW]

```java
// 复用自订单履约上下文
enum PaymentMethod {
    WECHAT,    // 微信
    ALIPAY,    // 支付宝
    BALANCE    // 余额
}
```

### 3.3 领域服务

#### 支付服务 \[NEW]

```java
class PaymentService {
    private final PaymentRepository paymentRepository;
    
    // 创建支付
    Payment createPayment(OrderId orderId, BigDecimal amount, PaymentMethod paymentMethod) {
        Payment payment = Payment.create(orderId, amount, paymentMethod);
        return paymentRepository.save(payment);
    }
    
    // 处理支付成功回调
    void handlePaymentSuccess(PaymentId paymentId, String transactionId) {
        Payment payment = paymentRepository.findById(paymentId)
            .orElseThrow(() -&gt; new PaymentNotFoundException(paymentId));
        
        payment.succeed(transactionId);
        paymentRepository.save(payment);
    }
    
    // 处理支付失败回调
    void handlePaymentFailure(PaymentId paymentId, String reason) {
        Payment payment = paymentRepository.findById(paymentId)
            .orElseThrow(() -&gt; new PaymentNotFoundException(paymentId));
        
        payment.fail(reason);
        paymentRepository.save(payment);
    }
    
    // 发起退款
    void refund(PaymentId paymentId, String reason) {
        Payment payment = paymentRepository.findById(paymentId)
            .orElseThrow(() -&gt; new PaymentNotFoundException(paymentId));
        
        payment.refund(reason);
        paymentRepository.save(payment);
    }
}
```

- **职责描述**：支付流程的协调服务
- **跨聚合操作**：不跨聚合，操作单个Payment

### 3.4 仓储接口

#### 支付仓储 \[NEW]

```java
interface PaymentRepository {
    Payment save(Payment payment);
    Optional&lt;Payment&gt; findById(PaymentId id);
    Optional&lt;Payment&gt; findByOrderId(OrderId orderId);
}
```

- **管理的聚合**：Payment

### 3.5 领域事件

#### 支付成功事件 \[NEW]

```java
class PaymentSucceededEvent {
    private final PaymentId paymentId;
    private final OrderId orderId;
    private final String transactionId;
    private final LocalDateTime occurredAt;
}
```

- **触发时机**：支付成功回调
- **订阅者**：订单上下文（更新订单状态）

#### 支付失败事件 \[NEW]

```java
class PaymentFailedEvent {
    private final PaymentId paymentId;
    private final OrderId orderId;
    private final String reason;
    private final LocalDateTime occurredAt;
}
```

- **触发时机**：支付失败回调
- **订阅者**：订单上下文（取消订单）

#### 退款成功事件 \[NEW]

```java
class PaymentRefundedEvent {
    private final PaymentId paymentId;
    private final OrderId orderId;
    private final String reason;
    private final LocalDateTime occurredAt;
}
```

- **触发时机**：退款成功
- **订阅者**：订单上下文（通知）

## 4. 模块划分建议

```
com.example.demoai.payment.
├── model.
│   ├── aggregate.
│   │   └── Payment
│   └── valueobject.
│       ├── PaymentId
│       ├── PaymentStatus
│       └── PaymentMethod
├── service.
│   └── PaymentService
├── repository.
│   └── PaymentRepository
└── event.
    ├── PaymentSucceededEvent
    ├── PaymentFailedEvent
    └── PaymentRefundedEvent
```

***

## 变更日志

| 版本   | 日期         | 变更描述                    |
| ---- | ---------- | ----------------------- |
| v1.0 | 2026-05-10 | 初始版本，完成支付聚合、值对象、领域服务的设计 |

