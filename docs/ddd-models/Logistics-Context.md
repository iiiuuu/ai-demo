# Logistics Context - 物流上下文领域模型设计

**文档状态**：NEW (外部上下文，当前阶段仅定义交互协议)

## 1. 上下文概述

- **限界上下文名称**：物流上下文 (Logistics Context)
- **类型**：支撑域 (Supporting Domain)
- **主要职责**：发货处理、物流单号管理、物流状态跟踪
- **边界范围**：发货单管理、物流公司对接、物流轨迹查询
- **当前阶段**：作为外部上下文，Order Context 通过领域事件与其交互，暂不实现内部细节

## 2. 与 Order Context 的交互协议

### 领域事件

| 事件 | 方向 | 说明 |
|-----|------|------|
| OrderPaidEvent | Order → Logistics | 触发发货流程 |

### 防腐层接口（Order Context 侧）

```java
public interface LogisticsService {
    void createShipment(ShipmentRequest request);
}

public class ShipmentRequest {
    private String orderNo;
    private ShippingAddress address;
    private List<ShipmentItem> items;
}
```

---

## 变更日志

| 版本 | 日期 | 变更描述 |
|-----|-----|---------|
| v1.0 | 2026-05-11 | 初始版本：定义物流上下文交互协议 |