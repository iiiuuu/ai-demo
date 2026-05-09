# 项目 DDD 四层架构指南 (Architecture Guide)

## 核心原则:
本项目**严格**遵守DDD的四层架构，**一切基于领域，一切为了领域**
- **Domain层(核心)**：包含全部业务侧的规则以及逻辑，不需要与任何技术框架耦合.
- **层与层之间单项依赖**: 上层依赖于下层，**禁止**反向依赖.
- **接口定义与实现分离**: Domain层定义接口,Infrastructure层提供实现.
- **跨层数据对象隔离**：每一层都拥有自己的数据对象，**禁止**跨层直接传递实例或者数据库实体，必须使用**MapStruct**进行对象转换.
- 
## 层依赖图(缰绳):
这个必须可在agent的脑子里，**任何违反这个依赖的代码都不允许生成**
```markdown
Interface层 --> Application层 --> Domain层 <-- Infrastructure层
```
**Domain层是通过接口反向依赖Infrastructure层的实现**
- Interface层: Controllers, DTOs (正向:使用MapStruct将requestDto转换为Command, 反向：接收application层返回的BO对象，通过MapperStruct转换为ResponseDto返回给用户)
- Application层: Application Services, Commands, BO (正向：使用MapStruct将Commands转化为DO,将反向: 通过MapperStruct将DO转化为BO.返回给Interface层)
- Domain层: Entities, Value Objects, Domain Services, Repository Interface (不需要使用MapStruct,保持纯粹性)
- Infrastructure层: Repository Impl, MyBatis-Plus Mapper, 数据对象(PO) (正向: 通过MapperStruct通过将DO转化为PO,反向：通过MapperStruct将PO转化为DO)

## 依赖规则表(硬性约束):

| 分层                 | 可依赖的分层                     | 绝对禁止依赖                                 | 对象转换职责(MapStruct)               |
|:-------------------|:---------------------------|:---------------------------------------|:--------------------------------|
| **Interface**      | Application(及其DTO/Command) | Domain, Infrastructure                 | 将外部请求DTO转换为Application层的Command |
| **Application**    | Domain(实体、接口)              | Infrastructure(具体实现)                   | 将Command转化为DO, 将DO转化为DTO并返回     |
| **Domain**         | **无**(仅依赖JDK和通用注解)         | Application, Interface, Infrastructure | **禁止**使用MapStruct，保持纯粹性         |
| **Infrastructure** | Domain(实现其接口)              | Application, Interface                 | 将PO与DO互相转换                      |

## 各层详细职责与标准实践
1. Interface(用户接口层) - `com.example.demoai.interface`

   **包结构示例**: `com.example.demoai.interface.controller`, `.dto`, `.assembler`

- **职责**:
    - 接口Http请求，进行基础的参数校验(格式，非空等)
    - 通过**MapStruct Mapper接口**(在本层assembler包内),
      - 1. 正向: 将RequestDTO转化为Application层的Command对象
      - 2. 反向: 接收Application层返回的BO对象，通过MapperStruct转换为ResponseDto返回给用户
    - 调用Application层的服务
    - 将返回结果封装为HTTP响应
- **可以做**:
    - 包含`@RestController`, `@RequestMapping`等Spring Web注解
    - 包含`@Valid`注解进行DTO的基础验证
    - 定义`@Mapper(componentModel = "spring")`的MapStruct接口
    - 定义Interface层的RequestDTO对象，作为从外部请求传入的载体
    - 定义Interface层的ResponseDto对象，作为返回给外部的载体
- **绝对禁止**:
  - 包含任何业务逻辑
  - 直接调用Domain层，Infrastructure层的任何类
  - 使用`BeanUtils.copyProperties`或者手动逐字段赋值进行对象转换
  - 直接返回BO对象
  
2. Application(应用层) - `com.example.demoai.application`

    **包结构示例**: `com.example.demoai.application.service`, `.command`, `.BO` ,`.assembler`

- **职责**:
    - 作为系统的`任务协调者`,不把任何的业务逻辑
    - 接收来自Interface层的Command对象
    - 对象转化
        - 1. 正向: 将Application层的Command对象转化为DO对象,通过**MapperStruct**进行转化
        - 2. 反向: 将Domain层的DO对象转化为BO对象,返回给Interface层，通过**MapperStruct**进行转化
    - 从Domain层获取领域对象，并调用其方法来执行业务逻辑
- **可以做**:
    - 包含`@Service`, `@Transactional`等Spring注解
    - 定义`@Mapper(componentModel = "spring")`的MapStruct接口
    - 定义Application层的BO对象，作为返回给Interface层的载体
    - 定义Application层的Command对象，作为从Interface层传入的载体
    - 调用Domain层的DO对象或者DomainService执行业务逻辑
- **绝对禁止**:
  - 包含任何核心的业务规则
  - 直接注入MyBatis-Plus的Mapper
  - 直接调用Domain层的Repository接口的实现类
  - 直接调用Infrastructure层的任何类
  - 直接返回DO对象 
  
3. Domain(领域层) - `com.example.demoai.domain`

    **包结构示例**: `com.example.demoai.domain.model.entity`, `.model.aggregate`, `.model.valueobject`, `.service`, `.repository`
- **职责**:
    - 项目核心，包含所有的业务概念，规则和逻辑
    - model包下是领域对象，包含业务属性和行为, **与application和Infrastructure层的交互只仅限于aggregate的对象**
    - service包下的DomainService包含跨实体的业务逻辑
    - repository包下的Repository接口定义了领域对象的持久化接口，由Infrastructure层提供实现
- **可以做**:
    - 定义领域实体(Entity)，值对象(Value Object)，领域服务(Domain Service)，仓储接口(Repository Interface)
    - 定义领域异常，继承RuntimeException，并包含业务错误码和错误信息
    - 调用Infrastructure层提供的Repository接口进行数据持久化
- **绝对禁止**:
    - 与application和Infrastructure层交互使用，非aggregate的对象的领域对象
    - 使用MapStruct或者任何形式的对象转换工具
    - 导入任何 MapStruct、MyBatis-Plus、Spring Web 的类。
    - **贫血模型**，只提供getter/setter
    - **充血模型**，实体必须有行为
4. Infrastructure(基础设施层) - `com.example.demoai.infrastructure`

    **包结构示例**: `com.example.demoai.domain.persistence.mapper`, `.persistence.repository`, `.persistence.assembler`, `.persistence.PO`, `.config`
- **职责**:
    - 封装所有技术框架相关的实现细节，如访问数据库，调用外部服务等
    - 通过 **MapStruct Mapper 接口**（在本层 `persistence.assembler` 包内），将持久化对象 (PO) 与 DO 互相转换。
    - `.config` 包下可以包含一些基础设施层的配置类，如 MyBatis-Plus 的分页插件配置等
- **可以做**:
    - 包含MyBatis-Plus的Mapper接口和XML文件
    - 包含Repository接口的实现类，注入MyBatis-Plus的Mapper并
    - 定义`@Mapper(componentModel = "spring")`的MapStruct接口，完成Domain Entity与持久化对象(PO)之间的转换
    - 定义持久化对象(PO)，作为与数据库交互的载体
    - 实现Domain层定义的Repository接口，完成数据持久化
- **绝对禁止**:
    - 包含任何业务逻辑
    - 调用任何其他层的类，除了Domain层定义的Repository接口
    - 将PO直接泄露到Domain层或者Application层
## 模块和包结构映射示例(含MapStruct)
假设有一个“订单”上下文，其代码结构应如下所示：
```markdown
com.example.demoai
├── interface
│ ├── controller
│ │ └── OrderController.java
│ ├── dto
│ │ ├── OrderRequestDTO.java // 请求 DTO
│ │ └── OrderResponseDTO.java // 响应 DTO
│ └── assembler
│   └── OrderInterfaceMapper.java // MapStruct: Request DTO ↔ Application Command
├── application
│ ├── service
│ │ └── OrderApplicationService.java
│ ├── command
│ │ └── CreateOrderCommand.java // 应用层命令对象
│ ├── BO
│ │ └── OrderBO.java // 应用层返回的 BO
│ └── assembler 
│   └── OrderApplicationMapper.java // MapStruct: DO -> DTO
├── domain
│ ├── model
│ │ ├── aggregate
│ │ │ └── Order.java // 聚合根 (禁止 MapStruct)
│ │ └── valueobject
│ │   └── OrderItem.java // 值对象 (禁止 MapStruct)
│ ├── service
│ │ └── OrderDomainService.java
│ └── repository
│   └── OrderRepository.java // 仓库接口 
└── infrastructure
│ ├── persistence
│ │ ├── mapper
│ │   └── OrderMapper.java // MyBatis-Plus Mapper
│ │ ├── PO
│ │   └── OrderPO.java // 持久化对象
│ │ ├── assembler
│ │   └── OrderPersistenceMapper.java // MapStruct: DO ↔ PO
│ │ ├── repository
│ │   └── OrderRepositoryImpl.java
│ ├──config
    └── MybatisPlusConfig.java
```

**MapStruct 接口定义铁律**：
-   每个层的 `assembler` 包下的 Mapper 接口，**只能**处理本层与其直接依赖层之间的对象转换。
-   例如，Interface 层的 Mapper 不能处理 DO ↔ PO 的转换。
-   所有 MapStruct 接口必须使用 `@Mapper(componentModel = "spring")` 注解。

---

此文档是项目的活宪法。AI Agent 生成的任何代码，都必须经过 `arch-unit-constraints-test.groovy` 的验证，以确保不偏离此架构。
