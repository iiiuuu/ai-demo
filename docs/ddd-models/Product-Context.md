
# 商品上下文 - 领域模型设计

**文档状态**：NEW

## 1. 上下文概述
- **限界上下文名称**：商品
- **主要职责**：商品信息管理，包括商品创建、更新、查询，价格管理
- **边界范围**：商品基本信息维护，商品价格查询

## 2. 聚合设计

### 2.1 商品 [NEW]
- **聚合根**：Product
- **不变性约束**：商品名称不能为空，价格必须大于0
- **包含实体/值对象**：无

## 3. 领域模型详细设计

### 3.1 实体

#### 商品 [NEW]
```java
class Product {
    private ProductId id;
    private String name;
    private String description;
    private BigDecimal price;
    private String imageUrl;
    private ProductStatus status;
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
    
    // 更新价格
    void updatePrice(BigDecimal newPrice) {
        assert newPrice.compareTo(BigDecimal.ZERO) &gt; 0;
        this.price = newPrice;
        this.updatedAt = now();
        publishEvent(ProductPriceUpdatedEvent.of(this.id, newPrice));
    }
    
    // 上架
    void activate() {
        this.status = ProductStatus.ACTIVE;
        this.updatedAt = now();
        publishEvent(ProductActivatedEvent.of(this.id));
    }
    
    // 下架
    void deactivate() {
        this.status = ProductStatus.INACTIVE;
        this.updatedAt = now();
        publishEvent(ProductDeactivatedEvent.of(this.id));
    }
}
```
- **唯一标识**：ProductId
- **关键行为**：创建、更新价格、上架、下架

### 3.2 值对象

#### 商品ID [NEW]
```java
class ProductId {
    private final String value;
}
```

#### 商品状态 [NEW]
```java
enum ProductStatus {
    ACTIVE,    // 上架
    INACTIVE   // 下架
}
```

### 3.3 领域服务

#### 商品查询服务 [NEW]
```java
class ProductQueryService {
    private final ProductRepository productRepository;
    
    // 查询商品信息（用于订单创建）
    ProductInfo getProductInfo(ProductId productId) {
        Product product = productRepository.findById(productId)
            .orElseThrow(() -&gt; new ProductNotFoundException(productId));
        
        assert product.getStatus() == ProductStatus.ACTIVE;
        
        return ProductInfo.of(
            product.getId(),
            product.getName(),
            product.getPrice()
        );
    }
    
    // 批量查询商品信息
    List&lt;ProductInfo&gt; getProductInfos(List&lt;ProductId&gt; productIds) {
        List&lt;Product&gt; products = productRepository.findByIds(productIds);
        
        return products.stream()
            .filter(p -&gt; p.getStatus() == ProductStatus.ACTIVE)
            .map(p -&gt; ProductInfo.of(p.getId(), p.getName(), p.getPrice()))
            .collect(toList());
    }
}
```
- **职责描述**：提供商品信息查询服务
- **跨聚合操作**：查询多个Product聚合

### 3.4 仓储接口

#### 商品仓储 [NEW]
```java
interface ProductRepository {
    Product save(Product product);
    Optional&lt;Product&gt; findById(ProductId id);
    List&lt;Product&gt; findByIds(List&lt;ProductId&gt; ids);
}
```
- **管理的聚合**：Product

### 3.5 领域事件

#### 商品创建事件 [NEW]
```java
class ProductCreatedEvent {
    private final ProductId productId;
    private final LocalDateTime occurredAt;
}
```
- **触发时机**：商品创建成功
- **订阅者**：库存上下文（初始化库存）

#### 商品价格更新事件 [NEW]
```java
class ProductPriceUpdatedEvent {
    private final ProductId productId;
    private final BigDecimal newPrice;
    private final LocalDateTime occurredAt;
}
```
- **触发时机**：商品价格更新
- **订阅者**：搜索索引更新

#### 商品上架事件 [NEW]
```java
class ProductActivatedEvent {
    private final ProductId productId;
    private final LocalDateTime occurredAt;
}
```
- **触发时机**：商品上架
- **订阅者**：搜索索引更新

#### 商品下架事件 [NEW]
```java
class ProductDeactivatedEvent {
    private final ProductId productId;
    private final LocalDateTime occurredAt;
}
```
- **触发时机**：商品下架
- **订阅者**：搜索索引更新

## 4. 模块划分建议

```
com.example.demoai.product.
├── model.
│   ├── aggregate.
│   │   └── Product
│   └── valueobject.
│       ├── ProductId
│       └── ProductStatus
├── service.
│   └── ProductQueryService
├── repository.
│   └── ProductRepository
└── event.
    ├── ProductCreatedEvent
    ├── ProductPriceUpdatedEvent
    ├── ProductActivatedEvent
    └── ProductDeactivatedEvent
```

---

## 变更日志

| 版本 | 日期 | 变更描述 |
|-----|-----|---------|
| v1.0 | 2026-05-10 | 初始版本，完成商品聚合、值对象、领域服务的设计 |
