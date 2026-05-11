# Inventory Context - 库存上下文领域模型设计

**文档状态**：NEW (外部上下文，当前阶段仅定义交互协议)

## 1. 上下文概述

- **限界上下文名称**：库存上下文 (Inventory Context)
- **类型**：支撑域 (Supporting Domain)
- **主要职责**：管理商品库存的扣减与释放
- **边界范围**：库存数量管理、库存预留、库存释放
- **当前阶段**：作为外部上下文，Order Context 通过领域事件与其交互，暂不实现内部细节

## 2. 与 Order Context 的交互协议

### 领域事件

| 事件 | 方向 | 说明 |
|-----|------|------|
| OrderCreatedEvent | Order → Inventory | 触发库存扣减 |
| OrderCancelledEvent | Order → Inventory | 触发库存释放 |

### 防腐层接口（Order Context 侧）

```java
public interface InventoryService {
    void deduct(List<InventoryItem> items);
    void release(List<InventoryItem> items);
}

public class InventoryItem {
    private Long productId;
    private Integer quantity;
}
```

---

## 变更日志

| 版本 | 日期 | 变更描述 |
|-----|-----|---------|
| v1.0 | 2026-05-11 | 初始版本：定义库存上下文交互协议 |