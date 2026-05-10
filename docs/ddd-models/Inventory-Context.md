
# 库存上下文 - 领域模型设计

**文档状态**：NEW

## 1. 上下文概述
- **限界上下文名称**：库存
- **主要职责**：商品库存管理，包括库存扣减、释放、查询
- **边界范围**：库存记录维护，库存操作记录

## 2. 聚合设计

### 2.1 库存 [NEW]
- **聚合根**：Inventory
- **不变性约束**：库存数量不能为负
- **包含实体/值对象**：InventoryRecord（库存扣减/释放记录）

## 3. 领域模型详细设计

### 3.1 实体

#### 库存 [NEW]
```java
class Inventory {
    private InventoryId id;
    private ProductId productId;
    private Integer totalQuantity;
    private Integer availableQuantity;
    private Integer lockedQuantity;
    private List&lt;InventoryRecord&gt; records;
    
    // 扣减库存
    void deduct(Integer quantity, OrderId orderId) {
        assert availableQuantity &gt;= quantity;
        this.availableQuantity -= quantity;
        this.lockedQuantity += quantity;
        this.records.add(InventoryRecord.deduct(productId, quantity, orderId));
        publishEvent(InventoryDeductedEvent.of(productId, quantity, orderId));
    }
    
    // 释放库存
    void release(Integer quantity, OrderId orderId) {
        this.lockedQuantity -= quantity;
        this.availableQuantity += quantity;
        this.records.add(InventoryRecord.release(productId, quantity, orderId));
        publishEvent(InventoryReleasedEvent.of(productId, quantity, orderId));
    }
    
    // 确认扣减（发货后）
    void confirmDeduction(Integer quantity, OrderId orderId) {
        this.lockedQuantity -= quantity;
        this.totalQuantity -= quantity;
        this.records.add(InventoryRecord.confirm(productId, quantity, orderId));
    }
}
```
- **唯一标识**：InventoryId
- **关键行为**：扣减、释放、确认扣减

#### 库存记录 [NEW]
```java
class InventoryRecord {
    private InventoryRecordId id;
    private ProductId productId;
    private Integer quantity;
    private InventoryAction action;  // DEDUCT, RELEASE, CONFIRM
    private OrderId orderId;
    private LocalDateTime occurredAt;
    
    static InventoryRecord deduct(ProductId productId, Integer quantity, OrderId orderId) {
        return new InventoryRecord(productId, quantity, InventoryAction.DEDUCT, orderId, now());
    }
    
    static InventoryRecord release(ProductId productId, Integer quantity, OrderId orderId) {
        return new InventoryRecord(productId, quantity, InventoryAction.RELEASE, orderId, now());
    }
    
    static InventoryRecord confirm(ProductId productId, Integer quantity, OrderId orderId) {
        return new InventoryRecord(productId, quantity, InventoryAction.CONFIRM, orderId, now());
    }
}
```

### 3.2 值对象

#### 库存ID [NEW]
```java
class InventoryId {
    private final String value;
}
```

#### 商品ID [NEW]
```java
class ProductId {
    private final String value;
}
```

#### 库存操作类型 [NEW]
```java
enum InventoryAction {
    DEDUCT,    // 扣减
    RELEASE,   // 释放
    CONFIRM    // 确认扣减
}
```

### 3.3 领域服务

#### 库存服务 [NEW]
```java
class InventoryService {
    private final InventoryRepository inventoryRepository;
    
    // 批量扣减库存（订单创建时）
    void deductInventory(OrderId orderId, List&lt;DeductItem&gt; items) {
        for (DeductItem item : items) {
            Inventory inventory = inventoryRepository.findByProductId(item.getProductId())
                .orElseThrow(() -&gt; new InventoryNotFoundException(item.getProductId()));
            
            inventory.deduct(item.getQuantity(), orderId);
            inventoryRepository.save(inventory);
        }
    }
    
    // 批量释放库存（订单取消时）
    void releaseInventory(OrderId orderId, List&lt;ReleaseItem&gt; items) {
        for (ReleaseItem item : items) {
            Inventory inventory = inventoryRepository.findByProductId(item.getProductId())
                .orElseThrow(() -&gt; new InventoryNotFoundException(item.getProductId()));
            
            inventory.release(item.getQuantity(), orderId);
            inventoryRepository.save(inventory);
        }
    }
    
    // 批量确认库存扣减（订单发货后）
    void confirmInventory(OrderId orderId, List&lt;ConfirmItem&gt; items) {
        for (ConfirmItem item : items) {
            Inventory inventory = inventoryRepository.findByProductId(item.getProductId())
                .orElseThrow(() -&gt; new InventoryNotFoundException(item.getProductId()));
            
            inventory.confirmDeduction(item.getQuantity(), orderId);
            inventoryRepository.save(inventory);
        }
    }
}
```
- **职责描述**：处理库存相关的领域逻辑
- **跨聚合操作**：操作多个Inventory聚合

### 3.4 仓储接口

#### 库存仓储 [NEW]
```java
interface InventoryRepository {
    Inventory save(Inventory inventory);
    Optional&lt;Inventory&gt; findById(InventoryId id);
    Optional&lt;Inventory&gt; findByProductId(ProductId productId);
}
```
- **管理的聚合**：Inventory

### 3.5 领域事件

#### 库存扣减事件 [NEW]
```java
class InventoryDeductedEvent {
    private final ProductId productId;
    private final Integer quantity;
    private final OrderId orderId;
    private final LocalDateTime occurredAt;
}
```
- **触发时机**：库存扣减成功
- **订阅者**：订单上下文（确认）

#### 库存释放事件 [NEW]
```java
class InventoryReleasedEvent {
    private final ProductId productId;
    private final Integer quantity;
    private final OrderId orderId;
    private final LocalDateTime occurredAt;
}
```
- **触发时机**：库存释放成功
- **订阅者**：订单上下文（确认）

## 4. 模块划分建议

```
com.example.demoai.inventory.
├── model.
│   ├── aggregate.
│   │   └── Inventory
│   ├── entity.
│   │   └── InventoryRecord
│   └── valueobject.
│       ├── InventoryId
│       ├── ProductId
│       └── InventoryAction
├── service.
│   └── InventoryService
├── repository.
│   └── InventoryRepository
└── event.
    ├── InventoryDeductedEvent
    └── InventoryReleasedEvent
```

---

## 变更日志

| 版本 | 日期 | 变更描述 |
|-----|-----|---------|
| v1.0 | 2026-05-10 | 初始版本，完成库存聚合、实体、值对象、领域服务的设计 |
