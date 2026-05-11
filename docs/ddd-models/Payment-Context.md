# Payment Context - 支付上下文领域模型设计

**文档状态**：NEW (外部上下文，当前阶段仅定义交互协议)

## 1. 上下文概述

- **限界上下文名称**：支付上下文 (Payment Context)
- **类型**：支撑域 (Supporting Domain)
- **主要职责**：处理支付请求、调用第三方支付网关（微信/支付宝/余额）、支付回调通知
- **边界范围**：支付记录、支付状态管理、第三方支付网关适配
- **当前阶段**：作为外部上下文，Order Context 通过领域事件与其交互，暂不实现内部细节

## 2. 与 Order Context 的交互协议

### 领域事件

| 事件 | 方向 | 说明 |
|-----|------|------|
| OrderCreatedEvent | Order → Payment | 触发支付流程 |
| PaymentCompletedEvent | Payment → Order | 通知支付结果 |

### 防腐层接口（Order Context 侧）

```java
public interface PaymentGateway {
    void requestPayment(PaymentRequest request);
}

public class PaymentRequest {
    private String orderNo;
    private Money amount;
    private PaymentMethod method;
}
```

---

## 变更日志

| 版本 | 日期 | 变更描述 |
|-----|-----|---------|
| v1.0 | 2026-05-11 | 初始版本：定义支付上下文交互协议 |