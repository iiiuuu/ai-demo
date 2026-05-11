# DDD设计文档格式规范

> **AI Agent必须严格遵守此格式生成DDD设计文档！**

## 📋 总览文档格式规范

### 必须包含的章节（按顺序）

```markdown
# {项目名} - DDD设计总览

## 1. 项目概述
{项目简介}

## 2. 限界上下文划分

| 限界上下文 | 主要职责 | 包含聚合 | 状态 |
|---------|---------|--------|------|
| {上下文1} | {职责} | {聚合} | NEW/UPDATED/EXISTING |
| {上下文2} | {职责} | {聚合} | NEW/UPDATED/EXISTING |

## 3. 上下文映射
{上下文关系描述}

## 4. 关键领域概念总结
{核心聚合、领域事件等}

---

## ⭐ 5. 待实现功能清单（按优先级） ⭐

### 优先级1：{限界上下文1}
1. **{功能点1名称}**
   - 描述：{功能描述}
   - 涉及层级：interface层、application层、domain层、infrastructure层
   - 优先级：P0
   - 参考文档：{限界上下文文档路径}

2. **{功能点2名称}**
   - 描述：{功能描述}
   - 涉及层级：interface层、application层、domain层、infrastructure层
   - 优先级：P1
   - 参考文档：{限界上下文文档路径}

### 优先级2：{限界上下文2}
1. **{功能点3名称}**
   - 描述：{功能描述}
   - 涉及层级：interface层、application层、domain层、infrastructure层
   - 优先级：P0
   - 参考文档：{限界上下文文档路径}

---

## 变更日志

| 版本 | 日期 | 变更描述 |
|-----|-----|---------|
| v{版本号} | {YYYY-MM-DD} | {描述} |
```

### 第5章节「待实现功能清单」要求
- **必须包含**，这是 ddd-developer 的输入来源
- 按优先级排序（P0 > P1 > P2）
- 每个功能点必须明确描述和涉及的层级
- **必须包含「参考文档」字段**，指定该功能点参考的限界上下文文档
- 功能点应该是垂直切片（从Interface到Domain完整的功能）

---

## 📋 限界上下文文档格式规范

### 📋 章节清单（必须包含，按顺序）

| # | 章节 | 说明 | 要求 |
|---|------|------|------|
| - | 标题与状态 | `# {限界上下文名称} - 领域模型设计` | 必须包含 `**文档状态**：NEW/UPDATED/EXISTING` |
| 1 | 上下文概述 | 说明限界上下文的名称、职责、边界 |  |
| 2 | 聚合设计 | 列出聚合根、实体、值对象 |  |
| 3 | 领域模型详细设计 | 详细设计实体、值对象、领域服务、仓储接口、领域事件 |  |
| 4 | ⭐ 待实现接口清单 ⭐ | 列出需要实现的接口 | **必须包含**，每个接口需包含：HTTP方法、路径、功能描述、RequestDTO、ResponseDTO、ApplicationService方法、涉及聚合 |
| 5 | ⭐ DTO/Command/BO 详细定义 ⭐ | 详细定义 Interface 层和 Application 层的数据对象 | **必须包含**，需完整定义字段和说明 |
| - | 变更日志 | 记录版本变更 |  |

---

### 📝 完整模板示例

````markdown
# {限界上下文名称} - 领域模型设计

**文档状态**：NEW/UPDATED/EXISTING

## 1. 上下文概述
- **限界上下文名称**：{名称}
- **主要职责**：{职责}
- **边界范围**：{范围}

## 2. 聚合设计
{聚合根、实体、值对象}

## 3. 领域模型详细设计
{实体、值对象、领域服务、仓储接口、领域事件}

---

## ⭐ 4. 待实现接口清单 ⭐

### 接口1：{接口名称}
- **HTTP方法**：POST/GET/PUT/DELETE
- **路径**：/api/{resource}
- **功能描述**：{描述}
- **RequestDTO**：{DTO名称}
- **ResponseDTO**：{DTO名称}
- **ApplicationService方法**：{方法名}
- **涉及聚合**：{聚合名}

### 接口2：{接口名称}
- **HTTP方法**：POST/GET/PUT/DELETE
- **路径**：/api/{resource}/{id}
- **功能描述**：{描述}
- **RequestDTO**：{DTO名称}
- **ResponseDTO**：{DTO名称}
- **ApplicationService方法**：{方法名}
- **涉及聚合**：{聚合名}

---

## ⭐ 5. DTO/Command/BO 详细定义 ⭐

### {限界上下文名} - Interface层

#### {RequestDTO1名称}
```java
// RequestDTO定义
class CreateOrderRequest {
    @NotNull
    Long userId;
    
    @NotEmpty
    List<OrderItemRequest> items;
    
    String remark;
}
```
- **字段验证规则**：userId必填、items非空
- **说明**：创建订单的请求参数

#### {ResponseDTO1名称}
```java
// ResponseDTO定义
class OrderResponse {
    Long orderId;
    String orderNo;
    BigDecimal totalAmount;
    OrderStatus status;
    LocalDateTime createTime;
}
```
- **说明**：订单详情响应

### {限界上下文名} - Application层

#### {Command1名称}
```java
// Command定义
class CreateOrderCommand {
    Long userId;
    List<OrderItemCommand> items;
    String remark;
}
```
- **字段验证规则**：userId必填、items非空
- **说明**：创建订单的应用层命令

#### {BO1名称}
```java
// BO定义
class OrderBO {
    Long orderId;
    String orderNo;
    BigDecimal totalAmount;
    OrderStatus status;
    LocalDateTime createTime;
}
```
- **说明**：订单业务对象，用于各层数据传递

---

## 变更日志

| 版本 | 日期 | 变更描述 |
|-----|-----|---------|
| v{版本号} | {YYYY-MM-DD} | {描述} |
````

---

## 📋 DTO/Command/BO 格式规范

### RequestDTO 格式

**标题：** `#### {RequestDTO名称} {[NEW]/[UPDATED]}`

**Java 代码示例：**
```java
// RequestDTO定义
class {RequestDTO名} {
    {字段列表，包含类型和注释}
}
```

**附加说明：**
- **字段验证规则**：{验证规则列表，如@NotNull、@Size等}
- **说明**：{该DTO的用途说明}

---

### ResponseDTO 格式

**标题：** `#### {ResponseDTO名称} {[NEW]/[UPDATED]}`

**Java 代码示例：**
```java
// ResponseDTO定义
class {ResponseDTO名} {
    {字段列表，包含类型和注释}
}
```

**附加说明：**
- **说明**：{该DTO的用途说明}

---

### Command 格式

**标题：** `#### {Command名称} {[NEW]/[UPDATED]}`

**Java 代码示例：**
```java
// Command定义
class {Command名} {
    {字段列表，包含类型和注释}
}
```

**附加说明：**
- **字段验证规则**：{验证规则列表}
- **说明**：{该Command的用途说明}

---

### BO 格式

**标题：** `#### {BO名称} {[NEW]/[UPDATED]}`

**Java 代码示例：**
```java
// BO定义
class {BO名} {
    {字段列表，包含类型和注释}
}
```

**附加说明：**
- **说明**：{该BO的用途说明}

---

## 📋 领域模型详细设计格式规范

### 实体格式

**标题：** `#### {实体名称} {[NEW]/[UPDATED]}`

**Java 代码示例：**
```java
// 实体定义
class {实体名} {
    {属性列表}
    
    // 关键行为
    {方法签名}
}
```

**附加说明：**
- **唯一标识**：{ID类型}
- **关键行为**：{业务方法列表}

---

### 值对象格式

**标题：** `#### {值对象名称} {[NEW]/[UPDATED]}`

**Java 代码示例：**
```java
// 值对象定义
class {值对象名} {
    {属性列表，全部为final}
    
    // 工厂方法
    static {值对象名} of(...) {
        ...
    }
}
```

**附加说明：**
- **不变性规则**：{不变性描述}
- **相等性依据**：{通过哪些属性判断相等}

---

### 领域服务格式

**标题：** `#### {领域服务名称} {[NEW]/[UPDATED]}`

**Java 代码示例：**
```java
// 领域服务定义
class {服务名} {
    {服务方法签名}
}
```

**附加说明：**
- **职责描述**：{服务的业务职责}
- **跨聚合操作**：{涉及哪些聚合}

---

### 仓储接口格式

**标题：** `#### {仓储接口名称} {[NEW]/[UPDATED]}`

**Java 代码示例：**
```java
// 仓储接口定义
interface {仓储名} {
    {接口方法列表}
}
```

**附加说明：**
- **管理的聚合**：{聚合根名称}

---

### 领域事件格式

**标题：** `#### {事件名称} {[NEW]/[UPDATED]}`

**Java 代码示例：**
```java
// 领域事件定义
class {事件名} {
    {事件属性，全部为final}
}
```

**附加说明：**
- **触发时机**：{何时发生此事件}
- **订阅者**：{可能的处理方}

---

## 🚨 红线规则（绝对不能违反）

| # | 规则 | 后果 |
|---|------|------|
| 1 | 总览文档必须包含第5章节「待实现功能清单」 | 文档不合格，需重新生成 |
| 2 | 限界上下文文档必须包含第4章节「待实现接口清单」 | 文档不合格，需重新生成 |
| 3 | 限界上下文文档必须包含第5章节「DTO/Command/BO详细定义」 | 文档不合格，需重新生成 |
| 4 | 章节顺序必须严格按照规范 | 文档不合格，需重新生成 |
| 5 | 每个功能点必须明确描述和涉及的层级 | 文档不合格，需补充 |
| 6 | 每个接口必须包含所有必填字段 | 文档不合格，需补充 |
| 7 | 每个DTO/Command/BO必须包含完整的字段定义 | 文档不合格，需补充 |
