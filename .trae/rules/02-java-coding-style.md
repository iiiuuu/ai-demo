# Java 编码规范 (Java Code Style)

本规范是本项目所有 Java 代码（包括领域逻辑、应用服务、基础设施实现）的**唯一风格指南**。AI Agent 产生的任何代码都必须通过本规范的自动检查

---

## 一、命名规范

### 1.1 包命名
- **全部小写**，使用单数形式。
- **严格**遵循 DDD 分层约定的包名：
    - `com.example.demoai.interfaces.controller`
    - `com.example.demoai.interfaces.assembler`
    - `com.example.demoai.application.service`
    - `com.example.demoai.application.assembler`
    - `com.example.demoai.domain.model.aggregate`
    - `com.example.demoai.domain.service`
    - `com.example.demoai.domain.repository`
    - `com.example.demoai.infrastructure.persistence.mapper`
    - `com.example.demoai.infrastructure.persistence.assembler`
    - `com.example.demoai.infrastructure.persistence.repository`

### 1.2 类与接口命名
- **实体 (Entity)**: 使用业务概念名，不加后缀。如 `Order`, `User`。
- **值对象 (Value Object)**: 不用特殊后缀，名字应体现其业务含义。如 `OrderItem`, `Address`。
- **领域服务 (Domain Service)**: 以 `Service` 结尾，名称体现其职责。如 `PointsDeductionService`。
- **仓库接口 (Repository Interface)**: 以 `Repository` 结尾。如 `OrderRepository`。**必须**定义在 `domain` 层。
- **应用服务 (Application Service)**: 以 `ApplicationService` 结尾。如 `OrderApplicationService`。
- **MyBatis-Plus Mapper**: 以 `Mapper` 结尾，定义在 `infrastructure` 层。如 `OrderMapper`。
- **仓库实现 (Repository Implementation)**: 以 `RepositoryImpl` 结尾，实现 `domain` 层的对应接口。如 `OrderRepositoryImpl`。
- **Controller**: 以 `Controller` 结尾，如 `OrderController`。
- **MapStruct 映射接口**: 以层名为前缀，以 `Mapper` 结尾，清晰表达转换方向。
    - Interface 层: `OrderInterfaceMapper`
    - Application 层: `OrderApplicationMapper`
    - Infrastructure 层: `OrderPersistenceMapper`
- **DTO,BO,PO**: 以业务概念名加上层级后缀，如 `OrderDTO`, `OrderBO`, `OrderPO`。

### 1.3 方法命名
- **MapStruct 映射方法**应直观体现源类型和目标类型，无需添加前缀。
    - 允许 `Order toEntity(CreateOrderCommand command);`
    - 允许 `OrderDTO toDto(Order entity);`
    - 禁止 `Order convertCommandToEntity(CreateOrderCommand command);` (冗余)

### 1.4 变量与常量
- 变量名使用有意义的业务词汇，避免单字母。
- 常量必须使用 `static final`，并全部大写，以下划线分隔。**禁止魔法值**。

## 二、DDD 编码铁律 —— 架构活着的保证

### 2.1 领域层：拒绝贫血模型
- 实体必须有行为。**绝对禁止**只提供 getter/setter。
- 实体构造器应尽量**明确和受控**，通过工厂方法或静态构造方法创建。

### 2.2 应用层：保持轻盈
- 应用服务方法体应短小（理想不超过 20 行），仅做编排：
    1. 使用 MapStruct 将 Command 转换为领域实体。
    2. 调用实体或领域服务方法。
    3. 使用 MapStruct 将实体转换为返回 DTO。
- **严禁**在应用层中出现表达业务规则的 `if-else`。

### 2.3 基础设施层：干净的实现
- **Repository 实现**必须注入 MyBatis-Plus 的 `BaseMapper` 和本层的 **MapStruct Mapper**，在内部完成 DO <-> PO 转换。

## 三、MyBatis-Plus 使用规范 —— 安全的桥梁

### 3.1 Mapper 的定义与使用
- 所有 `Mapper` 接口必须放在 `infrastructure.persistence.mapper` 包下，并使用 `@Mapper` 注解。
- 仅在 `RepositoryImpl` 中注入和调用 `Mapper`。

### 3.2 复杂查询
- 复杂查询必须在 XML 文件中定义 SQL。严禁在 Java 代码中通过 `QueryWrapper` 嵌套大量条件。

## 四、异常处理与日志 —— 透明的根基

### 4.1 异常
- **领域异常**：定义在 `domain` 层，继承 `RuntimeException`。
- **禁止吞异常**：catch 块必须做处理。

### 4.2 日志
- 使用 Lombok 的 `@Slf4j` 注解。
- 日志级别规则：error/warn/info/debug，对关键节点（如调用 MapStruct 转换失败时）记录异常详情。

## 五、Lombok 与通用工具

- MapStruct 生成的实现类无需手动编写，确保 `componentModel = "spring"`。

## 六、MapStruct 映射规范 —— 安全转换的生命线

### 6.1 核心铁律
- **所有跨层对象转换必须通过 MapStruct Mapper 接口完成**。
- **绝对禁止**使用 `BeanUtils.copyProperties`或手动逐字段赋值。
- 每个 MapStruct 接口必须使用 `@Mapper(componentModel = "spring")` 注解，确保被 Spring 管理。

### 6.2 各层 Mapper 的职责与存放位置

| 层                  | Mapper 接口位置                             | 职责                                                                     | 注入者                |
|:-------------------|:----------------------------------------|:-----------------------------------------------------------------------|:-------------------|
| **Interface**      | `interfaces/assembler/`                 | Request DTO -> Application Command <br/> Application BO-> Response DTO | Controller         |
| **Application**    | `application/assembler/`                | Command -> DO <br/> DO -> Application BO                               | ApplicationService |
| **Infrastructure** | `infrastructure/persistence/assembler/` | DO ↔ Persistence PO                                                    | RepositoryImpl     |

### 6.3 标准代码模板

#### Interface 层 Mapper 示例
```java
package com.example.demoai.interfaces.assembler;

import com.example.demoai.interfaces.dto.OrderRequest;
import com.example.demoai.application.command.CreateOrderCommand;
import org.mapstruct.Mapper;
import org.mapstruct.factory.Mappers;

@Mapper(componentModel = "spring")
public interface OrderInterfaceMapper {
    CreateOrderCommand toCommand(OrderRequest request);
    OrderResponseDTO toCommand(OrderBO orderBO);
}
```

#### Application 层 Mapper 示例
```java
package com.example.demoai.application.assembler;

import com.example.demoai.application.command.CreateOrderCommand;
import com.example.demoai.application.dto.OrderDTO;
import com.example.demoai.domain.model.aggregate.Order;
import org.mapstruct.Mapper;
import org.mapstruct.Mapping;

@Mapper(componentModel = "spring")
public interface OrderApplicationMapper {
    @Mapping(target = "id", ignore = true) // ID 由数据库生成
    @Mapping(target = "items", source = "items")
    Order toEntity(CreateOrderCommand command);

    OrderBO toBO(Order entity);
}
```
#### Infrastructure 层 Mapper 示例

```java
package com.example.demoai.infrastructure.persistence.assembler;

import com.example.demoai.domain.model.aggregate.Order;
import com.example.demoai.infrastructure.persistence.PO.OrderPO;
import org.mapstruct.Mapper;

@Mapper(componentModel = "spring")
public interface OrderPersistenceMapper {
    OrderPO toPO(Order entity);
    Order toEntity(OrderPO po);
}
```

### 6.4 注入与使用规范

+ 在需要转换的类中，直接注入对应的 MapStruct Mapper。
```java
@Autowired
private OrderApplicationMapper orderApplicationMapper;
```
+ 严禁通过 Mappers.getMapper() 获取实例，失去 Spring 管理可能导致依赖问题。
+ 转换表达式使用 MapStruct 内置的 source 和 expression

### 6.5 禁止事项
+ 禁止在 Domain 层引入 MapStruct 或任何转换接口。Domain 层保持纯粹，不接受外部对象转换。
+ 禁止 Mapper 接口之间互相调用或循环依赖。
+ 禁止 为了映射强行暴露实体的内部结构（如公开 setter）。如需映射特定字段，应使用 MapStruct 的 @Mapping 注解精确指定，同时保持实体封装。

## 七、代码生成规范 —— 正确书写符号

### 7.1 禁止使用 HTML/XML 实体编码符号

在任何代码文件（包括但不限于 Java、XML、Groovy、Markdown 等）中，**严令禁止**使用以下实体编码符号：

| **禁止的写法** | **必须使用的正确写法** | **说明** |
|--------------|------------------|---------|
| `&lt;`      | `<`              | 小于号   |
| `&gt;`      | `>`              | 大于号   |
| `&amp;`     | `&`              | 与号     |
| `&quot;`    | `"`              | 双引号   |
| `&apos;`    | `'`              | 单引号   |

#### 错误示例
```java
// ❌ 错误
if (a &lt; b) {  // 应该用 <
    // ...
}
List&lt;String&gt; list = new ArrayList&lt;&gt;();  // 应该用 < 和 >
```

#### 正确示例
```java
// ✅ 正确
if (a < b) {
    // ...
}
List<String> list = new ArrayList<>();
```

### 7.2 适用场景
- **所有代码文件**：Java 代码、Groovy 代码、XML 配置文件、Markdown 文档等
- **泛型定义**：`List<String>` 而非 `List&lt;String&gt;`
- **条件判断**：`if (a < b)` 而非 `if (a &lt; b)`
- **比较操作**：`> >= < <= == !=` 等
- **XML/HTML 标签**：仅在 XML/HTML 内容本身需要转义时使用，代码中一律不使用

## 八、自动化检查清单 (AI Agent 提交前自查)
在提交代码前，你必须逐项确认：

+ 没有魔法值，常量已定义。
+ 领域实体没有公开的 setter，行为方法命名体现业务。
+ 应用服务中没有 if-else 业务判断。
+ 没有在 domain 层导入 MapStruct、MyBatis-Plus 或 Spring MVC 的类。
+ 所有跨层对象转换都使用了 MapStruct，且 Mapper 所在层正确。
+ 所有 catch 块都有意义。
+ Mapper 未被 domain 层或 application 层直接引用。
+ 复杂 SQL 已放入 XML。
+ 日志级别正确，内容简洁但包含关键上下文。
+ **所有代码文件中不包含 `&lt;`、`&gt;` 等实体编码符号，一律使用 `<`、`>` 等原始符号**。