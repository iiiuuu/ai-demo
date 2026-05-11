# ai-demo - DDD设计总览

## 1. 项目概述

某外卖/电商平台的订单履约系统。用户下单后进入订单履约流程，涵盖订单创建、支付、发货、确认收货、取消等核心业务流程。系统需支持后续接入物流、退款、发票等扩展功能。

## 2. 限界上下文划分

| 限界上下文 | 类型 | 主要职责 | 包含聚合 | 状态 |
|---------|------|---------|---------|------|
| 订单上下文 (Order Context) | 核心域 | 订单创建、状态流转、金额计算、订单取消 | Order | NEW |
| 支付上下文 (Payment Context) | 支撑域 | 支付处理、支付回调通知 | Payment | NEW |
| 库存上下文 (Inventory Context) | 支撑域 | 库存扣减、库存释放 | Inventory | NEW |
| 物流上下文 (Logistics Context) | 支撑域 | 发货处理、物流跟踪 | Shipment | NEW |
| 商品上下文 (Product Context) | 支撑域 | 商品信息管理、价格查询 | Product | NEW |
| 用户上下文 (User Context) | 通用域 | 用户信息管理、收货地址管理 | User, Address | NEW |

## 3. 上下文映射

```
                    ┌──────────────┐
                    │  User Context │
                    │   (通用域)     │
                    └──────┬───────┘
                           │ ACL (读取用户/地址信息)
                           ▼
┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│Product Context│───▶│ Order Context │◀───│Payment Context│
│  (支撑域)     │ ACL │   (核心域)    │ PL │  (支撑域)     │
└──────────────┘    └──────┬───────┘    └──────────────┘
                           │ PL (领域事件)
              ┌────────────┼────────────┐
              ▼            ▼            ▼
    ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
    │Inventory Ctx │ │Logistics Ctx │ │  (未来扩展)   │
    │  (支撑域)     │ │  (支撑域)     │ │ 退款/发票等   │
    └──────────────┘ └──────────────┘ └──────────────┘
```

**上下文映射模式说明：**

| 上游 | 下游 | 映射模式 | 说明 |
|-----|-----|---------|------|
| Order Context | Payment Context | 发布语言 (PL) | 通过 OrderCreatedEvent 触发支付；Payment Context 通过 PaymentCompletedEvent 回调 |
| Order Context | Inventory Context | 发布语言 (PL) | 通过 OrderCreatedEvent 触发库存扣减；通过 OrderCancelledEvent 触发库存释放 |
| Order Context | Logistics Context | 发布语言 (PL) | 通过 OrderPaidEvent 触发发货流程 |
| Product Context | Order Context | 防腐层 (ACL) | Order Context 读取商品信息（名称、单价），隔离 Product Context 模型 |
| User Context | Order Context | 防腐层 (ACL) | Order Context 读取用户和收货地址信息，隔离 User Context 模型 |

## 4. 关键领域概念总结

### 核心聚合

| 聚合 | 所属上下文 | 聚合根 | 核心职责 |
|-----|---------|-------|---------|
| Order | Order Context | Order | 管理订单完整生命周期：创建→待支付→待发货→已发货→已完成/已取消 |
| Payment | Payment Context | Payment | 管理支付流程和支付记录 |
| Inventory | Inventory Context | Inventory | 管理商品库存的扣减与释放 |
| Shipment | Logistics Context | Shipment | 管理发货和物流跟踪 |

### 核心领域事件

| 领域事件 | 触发时机 | 消费者 |
|---------|---------|--------|
| OrderCreatedEvent | 订单创建成功 | Payment Context, Inventory Context |
| OrderPaidEvent | 支付成功 | Logistics Context |
| OrderShippedEvent | 商家发货 | (后续通知用户等) |
| OrderCompletedEvent | 确认收货 | (后续积分、评价等) |
| OrderCancelledEvent | 订单取消 | Inventory Context (释放库存) |
| PaymentCompletedEvent | 支付完成 | Order Context (更新订单状态) |

### 订单状态机

```
                    ┌──────────┐
                    │ 待支付    │──30分钟超时──▶ 已取消
                    │PENDING   │──用户取消───▶ (释放库存)
                    │_PAYMENT  │
                    └────┬─────┘
                         │ 支付成功
                         ▼
                    ┌──────────┐
                    │ 待发货    │
                    │PENDING   │
                    │_SHIPPING │
                    └────┬─────┘
                         │ 商家发货
                         ▼
                    ┌──────────┐
                    │ 已发货    │
                    │SHIPPED   │
                    └────┬─────┘
                         │ 用户确认收货
                         ▼
                    ┌──────────┐
                    │ 已完成    │
                    │COMPLETED │
                    └──────────┘
```

---

## ⭐ 5. 待实现功能清单（按优先级） ⭐

### 优先级1：订单上下文 (Order Context) — 核心域

1. **创建订单**
   - 描述：用户选择收货地址、支付方式、商品清单后创建订单，系统校验业务规则（金额不为负、优惠不超过商品总价），计算订单金额，订单状态初始化为待支付
   - 涉及层级：interface层、application层、domain层、infrastructure层
   - 优先级：P0
   - 参考文档：Order-Context.md

2. **支付成功回调处理**
   - 描述：接收支付上下文的支付成功通知，将订单状态从待支付变更为待发货，记录支付时间
   - 涉及层级：interface层、application层、domain层、infrastructure层
   - 优先级：P0
   - 参考文档：Order-Context.md

3. **商家发货**
   - 描述：商家发货后，将订单状态从待发货变更为已发货，记录发货时间
   - 涉及层级：interface层、application层、domain层、infrastructure层
   - 优先级：P0
   - 参考文档：Order-Context.md

4. **用户确认收货**
   - 描述：用户确认收货后，将订单状态从已发货变更为已完成，记录完成时间
   - 涉及层级：interface层、application层、domain层、infrastructure层
   - 优先级：P0
   - 参考文档：Order-Context.md

5. **取消订单**
   - 描述：用户取消待支付订单，校验只有待支付状态可取消，将订单状态变更为已取消，记录取消原因和时间，发布 OrderCancelledEvent 通知库存上下文释放库存
   - 涉及层级：interface层、application层、domain层、infrastructure层
   - 优先级：P0
   - 参考文档：Order-Context.md

6. **查询订单详情**
   - 描述：根据订单ID查询订单完整信息，包括订单项、收货地址、支付方式、金额明细、时间节点等
   - 涉及层级：interface层、application层、domain层、infrastructure层
   - 优先级：P0
   - 参考文档：Order-Context.md

7. **查询用户订单列表**
   - 描述：根据用户ID分页查询订单列表，支持按状态筛选
   - 涉及层级：interface层、application层、domain层、infrastructure层
   - 优先级：P1
   - 参考文档：Order-Context.md

8. **超时自动取消订单**
   - 描述：定时任务扫描超过30分钟未支付的待支付订单，自动取消，释放库存
   - 涉及层级：application层、domain层、infrastructure层
   - 优先级：P1
   - 参考文档：Order-Context.md

### 优先级2：支付上下文 (Payment Context) — 支撑域

9. **发起支付**
   - 描述：接收 OrderCreatedEvent，创建支付记录，调用第三方支付（微信/支付宝/余额）
   - 涉及层级：interface层、application层、domain层、infrastructure层
   - 优先级：P1
   - 参考文档：Order-Context.md（支付上下文暂不独立实现，在订单上下文中以领域事件方式预留扩展点）

### 优先级3：库存上下文 (Inventory Context) — 支撑域

10. **库存扣减**
    - 描述：接收 OrderCreatedEvent，扣减对应商品的库存
    - 涉及层级：application层、domain层、infrastructure层
    - 优先级：P2
    - 参考文档：Order-Context.md

11. **库存释放**
    - 描述：接收 OrderCancelledEvent，释放对应商品的库存
    - 涉及层级：application层、domain层、infrastructure层
    - 优先级：P2
    - 参考文档：Order-Context.md

---

## 变更日志

| 版本 | 日期 | 变更描述 |
|-----|-----|---------|
| v1.0 | 2026-05-11 | 初始版本：订单履约系统DDD建模，包含订单上下文完整设计 |