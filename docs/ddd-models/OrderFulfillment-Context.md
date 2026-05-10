
# 订单履约上下文 - 领域模型设计

**文档状态**：NEW

## 1. 上下文概述
- **限界上下文名称**：订单履约
- **主要职责**：管理订单全生命周期，包括创建、支付、发货、收货、取消，以及订单金额计算
- **边界范围**：订单创建、状态流转、金额计算、超时取消处理

## 2. 聚合设计

### 2.1 订单 [NEW]
- **聚合根**：Order
- **不变性约束**：
  - 创建时必须有收货地址、支付方式、商品清单
  - 金额不能为负
  - 优惠不能超过商品总价
  - 只有待支付状态可以取消
- **包含实体/值对象**：
  - OrderItem（订单商品项）
  - ShippingAddress（收货地址）
  - PaymentMethod（支付方式）
  - OrderAmount（订单金额）

## 3. 领域模型详细设计

### 3.1 实体

#### 订单 [NEW]
```java
// 实体定义
class Order {
    private OrderId id;
    private OrderStatus status;
    private ShippingAddress shippingAddress;
    private PaymentMethod paymentMethod;
    private List&lt;OrderItem&gt; items;
    private OrderAmount amount;
    private LocalDateTime createdAt;
    private LocalDateTime paidAt;
    private LocalDateTime shippedAt;
    private LocalDateTime deliveredAt;
    private LocalDateTime cancelledAt;
    
    // 关键行为
    void pay() {
        assertStatus(OrderStatus.PENDING_PAYMENT);
        this.status = OrderStatus.PENDING_SHIPMENT;
        this.paidAt = now();
        publishEvent(OrderPaidEvent.of(this.id));
    }
    
    void ship() {
        assertStatus(OrderStatus.PENDING_SHIPMENT);
        this.status = OrderStatus.SHIPPED;
        this.shippedAt = now();
        publishEvent(OrderShippedEvent.of(this.id));
    }
    
    void deliver() {
        assertStatus(OrderStatus.SHIPPED);
        this.status = OrderStatus.COMPLETED;
        this.deliveredAt = now();
        publishEvent(OrderDeliveredEvent.of(this.id));
    }
    
    void cancel(String reason) {
        assertStatus(OrderStatus.PENDING_PAYMENT);
        this.status = OrderStatus.CANCELLED;
        this.cancelledAt = now();
        publishEvent(OrderCancelledEvent.of(this.id, reason));
    }
}
```
- **唯一标识**：OrderId
- **关键行为**：创建、支付、发货、收货、取消

### 3.2 值对象

#### 订单ID [NEW]
```java
class OrderId {
    private final String value;
    
    // 工厂方法
    static OrderId generate() {
        return new OrderId(UUID.randomUUID().toString());
    }
    
    static OrderId of(String value) {
        return new OrderId(value);
    }
}
```
- **不变性规则**：不可变，值相等则对象相等
- **相等性依据**：value

#### 订单状态 [NEW]
```java
enum OrderStatus {
    PENDING_PAYMENT,  // 待支付
    PENDING_SHIPMENT, // 待发货
    SHIPPED,          // 已发货
    COMPLETED,        // 已完成
    CANCELLED         // 已取消
}
```

#### 收货地址 [NEW]
```java
class ShippingAddress {
    private final String recipientName;
    private final String phone;
    private final String province;
    private final String city;
    private final String district;
    private final String detailAddress;
}
```
- **不变性规则**：不可变
- **相等性依据**：所有字段

#### 支付方式 [NEW]
```java
enum PaymentMethod {
    WECHAT,    // 微信
    ALIPAY,    // 支付宝
    BALANCE    // 余额
}
```

#### 订单商品项 [NEW]
```java
class OrderItem {
    private final ProductId productId;
    private final String productName;
    private final BigDecimal unitPrice;
    private final Integer quantity;
    
    // 计算商品小计
    BigDecimal getSubtotal() {
        return unitPrice.multiply(BigDecimal.valueOf(quantity));
    }
}
```
- **不变性规则**：不可变，数量必须大于0
- **相等性依据**：productId

#### 订单金额 [NEW]
```java
class OrderAmount {
    private final BigDecimal productTotal;      // 商品总价
    private final BigDecimal shippingFee;       // 运费
    private final BigDecimal discount;          // 优惠
    private final BigDecimal totalAmount;       // 订单金额
    
    // 工厂方法，带校验
    static OrderAmount of(BigDecimal productTotal, 
                          BigDecimal shippingFee, 
                          BigDecimal discount) {
        // 金额不能为负
        assert productTotal.compareTo(BigDecimal.ZERO) &gt;= 0;
        assert shippingFee.compareTo(BigDecimal.ZERO) &gt;= 0;
        assert discount.compareTo(BigDecimal.ZERO) &gt;= 0;
        
        // 优惠不能超过商品总价
        assert discount.compareTo(productTotal) &lt;= 0;
        
        BigDecimal total = productTotal.add(shippingFee).subtract(discount);
        
        // 最终金额不能为负
        assert total.compareTo(BigDecimal.ZERO) &gt;= 0;
        
        return new OrderAmount(productTotal, shippingFee, discount, total);
    }
}
```
- **不变性规则**：不可变，金额校验逻辑封装在这里
- **相等性依据**：所有金额字段

### 3.3 领域服务

#### 订单金额计算器 [NEW]
```java
class OrderAmountCalculator {
    // 计算订单金额
    OrderAmount calculate(List&lt;OrderItem&gt; items, 
                          BigDecimal shippingFee, 
                          BigDecimal discount) {
        BigDecimal productTotal = items.stream()
            .map(OrderItem::getSubtotal)
            .reduce(BigDecimal.ZERO, BigDecimal::add);
        
        return OrderAmount.of(productTotal, shippingFee, discount);
    }
}
```
- **职责描述**：负责订单金额的复杂计算逻辑
- **跨聚合操作**：不跨聚合，纯计算

#### 订单超时服务 [NEW]
```java
class OrderTimeoutService {
    // 检查订单是否超时并取消
    void checkAndCancel(Order order) {
        if (order.getStatus() == OrderStatus.PENDING_PAYMENT &amp;&amp;
            order.getCreatedAt().plusMinutes(30).isBefore(now())) {
            order.cancel("支付超时自动取消");
        }
    }
}
```
- **职责描述**：处理订单超时取消逻辑
- **跨聚合操作**：不跨聚合，操作单个订单

### 3.4 仓储接口

#### 订单仓储 [NEW]
```java
interface OrderRepository {
    Order save(Order order);
    Optional&lt;Order&gt; findById(OrderId id);
    List&lt;Order&gt; findByStatus(OrderStatus status);
    List&lt;Order&gt; findPendingPaymentOrdersBefore(LocalDateTime time);
}
```
- **管理的聚合**：Order

### 3.5 领域事件

#### 订单创建事件 [NEW]
```java
class OrderCreatedEvent {
    private final OrderId orderId;
    private final List&lt;OrderItem&gt; items;
    private final LocalDateTime occurredAt;
}
```
- **触发时机**：订单创建成功
- **订阅者**：库存上下文（扣减库存）

#### 订单支付成功事件 [NEW]
```java
class OrderPaidEvent {
    private final OrderId orderId;
    private final LocalDateTime occurredAt;
}
```
- **触发时机**：用户支付成功
- **订阅者**：订单上下文（更新状态）、物流上下文（通知发货）

#### 订单发货事件 [NEW]
```java
class OrderShippedEvent {
    private final OrderId orderId;
    private final LocalDateTime occurredAt;
}
```
- **触发时机**：商家发货
- **订阅者**：物流上下文（更新物流信息）

#### 订单收货事件 [NEW]
```java
class OrderDeliveredEvent {
    private final OrderId orderId;
    private final LocalDateTime occurredAt;
}
```
- **触发时机**：用户确认收货
- **订阅者**：财务上下文（结算）

#### 订单取消事件 [NEW]
```java
class OrderCancelledEvent {
    private final OrderId orderId;
    private final String reason;
    private final LocalDateTime occurredAt;
}
```
- **触发时机**：订单取消（手动或超时）
- **订阅者**：库存上下文（释放库存）、支付上下文（退款）

## 4. 模块划分建议

```
com.example.demoai.orderfulfillment.
├── model.
│   ├── aggregate.
│   │   └── Order
│   ├── entity.
│   │   └── OrderItem
│   └── valueobject.
│       ├── OrderId
│       ├── OrderStatus
│       ├── ShippingAddress
│       ├── PaymentMethod
│       └── OrderAmount
├── service.
│   ├── OrderAmountCalculator
│   └── OrderTimeoutService
├── repository.
│   └── OrderRepository
└── event.
    ├── OrderCreatedEvent
    ├── OrderPaidEvent
    ├── OrderShippedEvent
    ├── OrderDeliveredEvent
    └── OrderCancelledEvent
```

---

## 变更日志

| 版本 | 日期 | 变更描述 |
|-----|-----|---------|
| v1.0 | 2026-05-10 | 初始版本，完成订单聚合、值对象、领域服务、仓储接口的设计 |
