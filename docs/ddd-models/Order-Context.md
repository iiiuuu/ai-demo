# Order Context - 订单上下文领域模型设计

**文档状态**：NEW

## 1. 上下文概述

- **限界上下文名称**：订单上下文 (Order Context)
- **类型**：核心域 (Core Domain)
- **主要职责**：管理订单的完整生命周期，包括订单创建、状态流转、金额计算、订单取消。所有核心业务规则在此上下文中内聚，不散落在代码各处。
- **边界范围**：订单聚合及其关联的订单项、收货地址、支付方式、金额计算逻辑

## 2. 聚合设计

### Order 聚合

```
┌─────────────────────────────────────────────────┐
│                  Order (聚合根)                    │
│  ┌─────────────────────────────────────────────┐ │
│  │  orderId, orderNo, userId                    │ │
│  │  status: OrderStatus                         │ │
│  │  productTotal, shippingFee, discount         │ │
│  │  totalAmount: Money                          │ │
│  │  createTime, payTime, shipTime               │ │
│  │  completeTime, cancelTime, cancelReason      │ │
│  └─────────────────────────────────────────────┘ │
│  ┌─────────────────────────────────────────────┐ │
│  │  OrderItem (实体) 1..*                       │ │
│  │  productId, productName, quantity            │ │
│  │  unitPrice, subtotal: Money                  │ │
│  └─────────────────────────────────────────────┘ │
│  ┌─────────────────────────────────────────────┐ │
│  │  ShippingAddress (值对象)                     │ │
│  │  receiverName, phone, province, city         │ │
│  │  district, detail                            │ │
│  └─────────────────────────────────────────────┘ │
│  ┌─────────────────────────────────────────────┐ │
│  │  PaymentMethod (值对象)                       │ │
│  │  type: WECHAT / ALIPAY / BALANCE             │ │
│  └─────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────┘
```

**聚合设计遵循4条铁律：**

| 铁律 | 实现方式 |
|-----|---------|
| 1. 聚合根是唯一外部入口 | 所有对 OrderItem、ShippingAddress 的操作必须通过 Order |
| 2. 一个事务只修改一个聚合根 | 库存扣减/释放通过领域事件异步处理 |
| 3. 聚合间通过ID引用 | Order 只存储 userId、productId，不持有其他聚合对象 |
| 4. 聚合根删除时级联删除 | 删除 Order 时同时删除所有 OrderItem |

## 3. 领域模型详细设计

### 3.1 聚合根

#### Order [NEW]

```java
public class Order {
    private Long orderId;
    private String orderNo;
    private Long userId;
    private OrderStatus status;
    private List<OrderItem> items;
    private ShippingAddress shippingAddress;
    private PaymentMethod paymentMethod;
    private Money productTotal;
    private Money shippingFee;
    private Money discount;
    private Money totalAmount;
    private LocalDateTime createTime;
    private LocalDateTime payTime;
    private LocalDateTime shipTime;
    private LocalDateTime completeTime;
    private LocalDateTime cancelTime;
    private String cancelReason;

    // 工厂方法：创建订单
    public static Order create(Long userId, ShippingAddress address,
                                PaymentMethod paymentMethod, List<OrderItem> items,
                                Money shippingFee, Money discount) {
        // 1. 校验商品清单非空
        // 2. 校验优惠不超过商品总价
        // 3. 计算金额
        // 4. 校验金额不为负
        // 5. 初始化状态为待支付
        // 6. 生成订单编号
        // 7. 发布 OrderCreatedEvent
    }

    // 支付成功
    public void pay() {
        // 校验当前状态为待支付
        // 变更状态为待发货
        // 记录支付时间
        // 发布 OrderPaidEvent
    }

    // 商家发货
    public void ship() {
        // 校验当前状态为待发货
        // 变更状态为已发货
        // 记录发货时间
        // 发布 OrderShippedEvent
    }

    // 确认收货
    public void confirmReceipt() {
        // 校验当前状态为已发货
        // 变更状态为已完成
        // 记录完成时间
        // 发布 OrderCompletedEvent
    }

    // 取消订单
    public void cancel(String reason) {
        // 校验当前状态为待支付
        // 变更状态为已取消
        // 记录取消原因和时间
        // 发布 OrderCancelledEvent
    }

    // 计算订单总金额
    private void calculateTotalAmount() {
        // totalAmount = productTotal + shippingFee - discount
        // 校验 totalAmount >= 0
        // 校验 discount <= productTotal
    }
}
```

- **唯一标识**：orderId (Long)，orderNo (String，业务编号)
- **关键行为**：create(工厂方法), pay(), ship(), confirmReceipt(), cancel(reason)
- **不变性约束**：
  - 订单金额不能为负
  - 优惠金额不能超过商品总价
  - 状态流转必须遵循状态机规则

### 3.2 实体

#### OrderItem [NEW]

```java
public class OrderItem {
    private Long productId;
    private String productName;
    private Integer quantity;
    private Money unitPrice;
    private Money subtotal;

    public static OrderItem create(Long productId, String productName,
                                    Integer quantity, Money unitPrice) {
        // 校验 quantity > 0
        // 校验 unitPrice >= 0
        // 计算 subtotal = unitPrice * quantity
    }
}
```

- **唯一标识**：productId（在订单聚合内唯一）
- **关键行为**：create(工厂方法)
- **说明**：订单项的生命周期完全依附于 Order 聚合根

### 3.3 值对象

#### OrderStatus [NEW]

```java
public enum OrderStatus {
    PENDING_PAYMENT("待支付"),
    PENDING_SHIPPING("待发货"),
    SHIPPED("已发货"),
    COMPLETED("已完成"),
    CANCELLED("已取消");

    private final String description;

    // 判断是否允许转换到目标状态
    public boolean canTransitionTo(OrderStatus target) {
        return switch (this) {
            case PENDING_PAYMENT -> target == PENDING_SHIPPING || target == CANCELLED;
            case PENDING_SHIPPING -> target == SHIPPED;
            case SHIPPED -> target == COMPLETED;
            case COMPLETED, CANCELLED -> false;
        };
    }
}
```

- **不变性规则**：枚举值不可变
- **相等性依据**：枚举值本身

#### Money [NEW]

```java
public class Money {
    private final BigDecimal amount;
    private final String currency;

    private Money(BigDecimal amount, String currency) {
        this.amount = amount;
        this.currency = currency;
    }

    public static Money of(BigDecimal amount) {
        return new Money(amount, "CNY");
    }

    public static Money zero() {
        return new Money(BigDecimal.ZERO, "CNY");
    }

    public Money add(Money other) {
        // 同币种相加
    }

    public Money subtract(Money other) {
        // 同币种相减
    }

    public Money multiply(int multiplier) {
        // 金额乘以数量
    }

    public boolean isNegative() {
        return amount.compareTo(BigDecimal.ZERO) < 0;
    }

    public boolean isGreaterThan(Money other) {
        return amount.compareTo(other.amount) > 0;
    }
}
```

- **不变性规则**：所有属性为 final，不可变
- **相等性依据**：amount + currency 都相等

#### ShippingAddress [NEW]

```java
public class ShippingAddress {
    private final String receiverName;
    private final String phone;
    private final String province;
    private final String city;
    private final String district;
    private final String detail;

    public ShippingAddress(String receiverName, String phone,
                           String province, String city,
                           String district, String detail) {
        // 校验必填字段非空
    }
}
```

- **不变性规则**：所有属性为 final，不可变
- **相等性依据**：所有属性都相等

#### PaymentMethod [NEW]

```java
public enum PaymentMethod {
    WECHAT("微信支付"),
    ALIPAY("支付宝"),
    BALANCE("余额支付");

    private final String description;
}
```

- **不变性规则**：枚举值不可变
- **相等性依据**：枚举值本身

### 3.4 领域服务

#### OrderDomainService [NEW]

```java
public class OrderDomainService {

    // 计算商品总价
    public Money calculateProductTotal(List<OrderItem> items) {
        // 累加所有 OrderItem.subtotal
    }

    // 校验优惠金额合法性
    public void validateDiscount(Money productTotal, Money discount) {
        // discount 不能超过 productTotal
    }

    // 校验订单金额合法性
    public void validateTotalAmount(Money totalAmount) {
        // totalAmount 不能为负
    }
}
```

- **职责描述**：处理跨实体的金额计算和校验逻辑，这些逻辑不属于单个实体
- **跨聚合操作**：无，仅在本聚合内操作

### 3.5 仓储接口

#### OrderRepository [NEW]

```java
public interface OrderRepository {
    Order save(Order order);
    Optional<Order> findById(Long orderId);
    Optional<Order> findByOrderNo(String orderNo);
    List<Order> findByUserId(Long userId, OrderStatus status, Pageable pageable);
    List<Order> findPendingPaymentBefore(LocalDateTime deadline);
}
```

- **说明**：定义在 Domain 层，由 Infrastructure 层实现

### 3.6 领域事件

| 领域事件 | 触发时机 | 携带数据 | 消费者 |
|---------|---------|---------|--------|
| OrderCreatedEvent | 订单创建成功 | orderId, userId, items(商品ID+数量), totalAmount, paymentMethod | Payment Context(发起支付), Inventory Context(扣减库存) |
| OrderPaidEvent | 支付成功 | orderId, payTime | Logistics Context(触发发货) |
| OrderShippedEvent | 商家发货 | orderId, shipTime | (后续：通知用户) |
| OrderCompletedEvent | 确认收货 | orderId, completeTime | (后续：积分、评价) |
| OrderCancelledEvent | 订单取消 | orderId, cancelReason, items(商品ID+数量) | Inventory Context(释放库存) |

---

## ⭐ 4. 待实现接口清单 ⭐

### 接口1：创建订单
- **HTTP方法**：POST
- **路径**：/api/orders
- **功能描述**：用户创建订单，传入收货地址ID、支付方式、商品清单，系统计算金额并创建订单
- **RequestDTO**：CreateOrderRequest
- **ResponseDTO**：OrderResponse
- **ApplicationService方法**：OrderApplicationService.createOrder(CreateOrderCommand)
- **涉及聚合**：Order

### 接口2：查询订单详情
- **HTTP方法**：GET
- **路径**：/api/orders/{orderId}
- **功能描述**：根据订单ID查询订单完整信息
- **RequestDTO**：无（路径参数）
- **ResponseDTO**：OrderResponse
- **ApplicationService方法**：OrderApplicationService.getOrder(Long orderId)
- **涉及聚合**：Order

### 接口3：查询用户订单列表
- **HTTP方法**：GET
- **路径**：/api/orders?userId={userId}&status={status}&page={page}&size={size}
- **功能描述**：分页查询用户订单列表，支持按状态筛选
- **RequestDTO**：无（查询参数）
- **ResponseDTO**：OrderListResponse
- **ApplicationService方法**：OrderApplicationService.listOrders(Long userId, OrderStatus status, Pageable pageable)
- **涉及聚合**：Order

### 接口4：取消订单
- **HTTP方法**：POST
- **路径**：/api/orders/{orderId}/cancel
- **功能描述**：用户取消待支付订单
- **RequestDTO**：CancelOrderRequest
- **ResponseDTO**：OrderResponse
- **ApplicationService方法**：OrderApplicationService.cancelOrder(Long orderId, CancelOrderCommand)
- **涉及聚合**：Order

### 接口5：支付回调
- **HTTP方法**：POST
- **路径**：/api/orders/{orderId}/pay-callback
- **功能描述**：接收支付网关回调，将订单状态变更为待发货
- **RequestDTO**：PayCallbackRequest
- **ResponseDTO**：OrderResponse
- **ApplicationService方法**：OrderApplicationService.handlePayCallback(Long orderId, PayCallbackCommand)
- **涉及聚合**：Order

### 接口6：商家发货
- **HTTP方法**：POST
- **路径**：/api/orders/{orderId}/ship
- **功能描述**：商家发货，将订单状态变更为已发货
- **RequestDTO**：ShipOrderRequest
- **ResponseDTO**：OrderResponse
- **ApplicationService方法**：OrderApplicationService.shipOrder(Long orderId, ShipOrderCommand)
- **涉及聚合**：Order

### 接口7：确认收货
- **HTTP方法**：POST
- **路径**：/api/orders/{orderId}/confirm-receipt
- **功能描述**：用户确认收货，将订单状态变更为已完成
- **RequestDTO**：ConfirmReceiptRequest
- **ResponseDTO**：OrderResponse
- **ApplicationService方法**：OrderApplicationService.confirmReceipt(Long orderId)
- **涉及聚合**：Order

---

## ⭐ 5. DTO/Command/BO 详细定义 ⭐

### Order Context - Interface层

#### CreateOrderRequest [NEW]

```java
public class CreateOrderRequest {
    @NotNull(message = "用户ID不能为空")
    private Long userId;

    @NotNull(message = "收货地址ID不能为空")
    private Long addressId;

    @NotNull(message = "支付方式不能为空")
    private String paymentMethod;

    @NotEmpty(message = "商品清单不能为空")
    @Size(min = 1, message = "至少选择一个商品")
    private List<OrderItemRequest> items;

    private String remark;
}
```

- **字段验证规则**：userId必填、addressId必填、paymentMethod必填（WECHAT/ALIPAY/BALANCE）、items非空且至少1项
- **说明**：创建订单的请求参数

#### OrderItemRequest [NEW]

```java
public class OrderItemRequest {
    @NotNull(message = "商品ID不能为空")
    private Long productId;

    @NotNull(message = "商品数量不能为空")
    @Min(value = 1, message = "商品数量至少为1")
    private Integer quantity;
}
```

- **字段验证规则**：productId必填、quantity必填且≥1
- **说明**：订单中单个商品的请求参数

#### CancelOrderRequest [NEW]

```java
public class CancelOrderRequest {
    @Size(max = 200, message = "取消原因最多200字")
    private String reason;
}
```

- **字段验证规则**：reason可选，最多200字
- **说明**：取消订单的请求参数

#### PayCallbackRequest [NEW]

```java
public class PayCallbackRequest {
    @NotNull(message = "支付流水号不能为空")
    private String transactionId;

    @NotNull(message = "支付状态不能为空")
    private String payStatus;

    private String payTime;
}
```

- **字段验证规则**：transactionId必填、payStatus必填
- **说明**：支付回调的请求参数

#### ShipOrderRequest [NEW]

```java
public class ShipOrderRequest {
    @NotEmpty(message = "物流单号不能为空")
    private String trackingNumber;

    @NotEmpty(message = "物流公司不能为空")
    private String logisticsCompany;
}
```

- **字段验证规则**：trackingNumber必填、logisticsCompany必填
- **说明**：发货的请求参数

#### ConfirmReceiptRequest [NEW]

```java
public class ConfirmReceiptRequest {
    // 预留扩展字段
}
```

- **说明**：确认收货的请求参数（当前无必填字段，预留扩展）

#### OrderResponse [NEW]

```java
public class OrderResponse {
    private Long orderId;
    private String orderNo;
    private Long userId;
    private String status;
    private String statusDesc;
    private List<OrderItemResponse> items;
    private ShippingAddressResponse shippingAddress;
    private String paymentMethod;
    private String paymentMethodDesc;
    private BigDecimal productTotal;
    private BigDecimal shippingFee;
    private BigDecimal discount;
    private BigDecimal totalAmount;
    private LocalDateTime createTime;
    private LocalDateTime payTime;
    private LocalDateTime shipTime;
    private LocalDateTime completeTime;
    private LocalDateTime cancelTime;
    private String cancelReason;
}
```

- **说明**：订单详情响应

#### OrderItemResponse [NEW]

```java
public class OrderItemResponse {
    private Long productId;
    private String productName;
    private Integer quantity;
    private BigDecimal unitPrice;
    private BigDecimal subtotal;
}
```

- **说明**：订单项响应

#### ShippingAddressResponse [NEW]

```java
public class ShippingAddressResponse {
    private String receiverName;
    private String phone;
    private String province;
    private String city;
    private String district;
    private String detail;
}
```

- **说明**：收货地址响应

#### OrderListResponse [NEW]

```java
public class OrderListResponse {
    private List<OrderResponse> orders;
    private Integer page;
    private Integer size;
    private Long total;
}
```

- **说明**：订单列表分页响应

### Order Context - Application层

#### CreateOrderCommand [NEW]

```java
public class CreateOrderCommand {
    private Long userId;
    private Long addressId;
    private PaymentMethod paymentMethod;
    private List<OrderItemCommand> items;
    private String remark;
}
```

- **字段验证规则**：userId必填、addressId必填、paymentMethod必填、items非空
- **说明**：创建订单的应用层命令

#### OrderItemCommand [NEW]

```java
public class OrderItemCommand {
    private Long productId;
    private Integer quantity;
}
```

- **字段验证规则**：productId必填、quantity必填且≥1
- **说明**：订单项命令

#### CancelOrderCommand [NEW]

```java
public class CancelOrderCommand {
    private String reason;
}
```

- **字段验证规则**：reason可选
- **说明**：取消订单命令

#### PayCallbackCommand [NEW]

```java
public class PayCallbackCommand {
    private String transactionId;
    private String payStatus;
    private LocalDateTime payTime;
}
```

- **字段验证规则**：transactionId必填、payStatus必填
- **说明**：支付回调命令

#### ShipOrderCommand [NEW]

```java
public class ShipOrderCommand {
    private String trackingNumber;
    private String logisticsCompany;
}
```

- **字段验证规则**：trackingNumber必填、logisticsCompany必填
- **说明**：发货命令

#### OrderBO [NEW]

```java
public class OrderBO {
    private Long orderId;
    private String orderNo;
    private Long userId;
    private OrderStatus status;
    private List<OrderItemBO> items;
    private ShippingAddress shippingAddress;
    private PaymentMethod paymentMethod;
    private Money productTotal;
    private Money shippingFee;
    private Money discount;
    private Money totalAmount;
    private LocalDateTime createTime;
    private LocalDateTime payTime;
    private LocalDateTime shipTime;
    private LocalDateTime completeTime;
    private LocalDateTime cancelTime;
    private String cancelReason;
}
```

- **说明**：订单业务对象，用于 Application 层与 Interface 层之间的数据传递

#### OrderItemBO [NEW]

```java
public class OrderItemBO {
    private Long productId;
    private String productName;
    private Integer quantity;
    private Money unitPrice;
    private Money subtotal;
}
```

- **说明**：订单项业务对象

---

## 变更日志

| 版本 | 日期 | 变更描述 |
|-----|-----|---------|
| v1.0 | 2026-05-11 | 初始版本：订单上下文领域模型设计，包含聚合设计、领域模型、接口清单、DTO/Command/BO定义 |