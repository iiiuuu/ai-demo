# Product Context - 商品上下文领域模型设计

**文档状态**：NEW (外部上下文，当前阶段仅定义交互协议)

## 1. 上下文概述

- **限界上下文名称**：商品上下文 (Product Context)
- **类型**：支撑域 (Supporting Domain)
- **主要职责**：商品信息管理、价格查询
- **边界范围**：商品基本信息、SKU管理、价格管理
- **当前阶段**：作为外部上下文，Order Context 通过防腐层读取商品信息，暂不实现内部细节

## 2. 与 Order Context 的交互协议

### 防腐层接口（Order Context 侧）

```java
public interface ProductService {
    ProductInfo getProduct(Long productId);
    List<ProductInfo> getProducts(List<Long> productIds);
}

public class ProductInfo {
    private Long productId;
    private String productName;
    private Money unitPrice;
}
```

---

## 变更日志

| 版本 | 日期 | 变更描述 |
|-----|-----|---------|
| v1.0 | 2026-05-11 | 初始版本：定义商品上下文交互协议 |