
# MapStruct 全攻略

## 🎯 版本要求
- **精确版本**: 1.5.5.Final

## 📁 文件位置要求

| 层 | 位置 | 命名 |
|------|----------|------|
| Interface | `interface.assembler` | `{领域}InterfaceMapper` |
| Application | `application.assembler` | `{领域}ApplicationMapper` |
| Infrastructure | `infrastructure.persistence.assembler` | `{领域}PersistenceMapper` |

## 🎯 使用规范

### 注解要求
- ✅ 必须使用 `@Mapper(componentModel = "spring")`
- ✅ 忽略字段时使用 `@Mapping(target = "xxx", ignore = true)`
- ✅ 必须添加注释说明忽略原因

### 各层Mapper职责

#### Interface层
```java
@Mapper(componentModel = "spring")
public interface OrderInterfaceMapper {
    CreateOrderCommand toCommand(OrderRequestDTO request);
    OrderResponseDTO toResponse(OrderBO orderBO);
}
```

#### Application层
```java
@Mapper(componentModel = "spring")
public interface OrderApplicationMapper {
    @Mapping(target = "id", ignore = true) // ID由数据库生成
    @Mapping(target = "orderItems", source = "items")
    Order toDO(CreateOrderCommand command);
    OrderBO toBO(Order order);
    List&lt;OrderBO&gt; toBOList(List&lt;Order&gt; orders);
}
```

#### Infrastructure层
```java
@Mapper(componentModel = "spring")
public interface OrderPersistenceMapper {
    Order toDO(OrderPO orderPO);
    OrderPO toPO(Order order);
}
```

### 映射关系规则
- ✅ 同名字段自动映射
- ✅ 不同名字段使用 `@Mapping(source="a", target="b")`
- ✅ 简单类型转换自动处理
- ✅ 复杂逻辑提取为独立工具类

### 禁止事项
- ❌ 禁止在Domain层使用MapStruct
- ❌ 禁止跨层映射（如Infrastructure层Mapper处理Command→PO）
- ❌ 禁止Mapper之间互相调用
- ❌ 禁止使用 `BeanUtils.copyProperties` 或手动赋值
- ❌ 禁止expression包含外部调用

## 🎯 AI常见错误
| 常见错误 | 修正方案 |
|---------|---------|
| 在实体上用@Builder/@Setter只是为了映射 | 实体用工厂方法，Mapper中标记ignore |
