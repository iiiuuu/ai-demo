---
name: "ddd-developer"
description: "根据 ddd-analyzer 生成的 DDD 设计文档进行代码开发，严格遵循 7阶段TDD 开发流程。支持增量开发，避免重复创建已有类。Invoke when docs/ddd-models/ 下有文档并且需要进行代码实现时"
---

# DDD开发者 Skill

## 功能概述

本skill用于根据 ddd-analyzer 生成的 DDD 设计文档进行代码开发，严格遵循项目的 7阶段TDD 开发流程。**支持增量开发**，避免重复创建已有类，只做最小必要改动。

## 前置依赖规范

本Skill执行过程中，必须严格遵循以下规则：
- [01-architecture-constraints.md](../../rules/01-architecture-constraints.md) - DDD架构约束
- [02-java-coding-style.md](../../rules/02-java-coding-style.md) - Java编码规范
- [03-spring-boot-guide.md](../../rules/03-spring-boot-guide.md) - Spring Boot全攻略
- [04-mybatis-plus-guide.md](../../rules/04-mybatis-plus-guide.md) - MyBatis-Plus全攻略
- [05-mapstruct-guide.md](../../rules/05-mapstruct-guide.md) - MapStruct全攻略
- [06-spock-testing-guide.md](../../rules/06-spock-testing-guide.md) - Spock测试全攻略
- [07-git-commit-message.md](../../rules/07-git-commit-message.md) - Git提交规范
- [08-development-workflow.md](../../rules/08-development-workflow.md) - 7阶段TDD开发流程（核心流程）

本Skill文档仅描述**在基础规范之上的DDD特有增强**，基础流程请参考上述规则文件。

## 相比基础规范的增强

本Skill在 [08-development-workflow.md](../../rules/08-development-workflow.md) 的基础上，增加了以下DDD开发特有的功能：

| 增强项 | 说明 |
|-------|------|
| 增量开发策略 | 检查现有代码库，避免重复创建 |
| 代码库现状检查 | 扫描 src/ 目录，对比设计与实现 |
| DDD专用任务拆解 | 按功能点拆解，明确各层职责 |
| 完整四层实现 | 从Domain到Interface完整实现，不遗漏 |

## 使用时机

### 触发条件

以下任一情况满足时，使用本skill：
1. 用户明确要求根据 DDD 设计文档进行代码实现 
2. `docs/ddd-models/` 目录下存在设计文档，且用户需要实现
3. 在 `ddd-analyzer` 执行成功后，用户继续要求代码实现

## 增量开发策略 ⚠️

### 第一步：代码库现状检查 📊

在开始开发前，必须检查：
1. **检查 `src/` 目录**
   - 列出已有的包结构
   - 识别已存在的类
2. **检查 Git 状态**
   - 查看当前分支
   - 查看是否有未提交的变更
3. **分析 DDD 文档变更**
   - 阅读文档中的变更日志
   - 识别：`[NEW]`、`[UPDATED]`、`[DEPRECATED]` 标记的内容

### 第二步：差异分析与任务制定 🔍

1. **对比设计与实现**
   - 识别：哪些类已存在，哪些不存在
   - 识别：哪些方法已存在，哪些需要新增/修改
2. **制定增量任务**
   - ✅ **新增任务**：创建新类、新方法
   - 🔄 **修改任务**：修改现有类、现有方法
   - ❌ **删除任务**：删除废弃的类、方法（谨慎操作）

### 第三步：最小改动原则

1. **只修改必要的**
   - 不要重构没有变更需求的代码
   - 保持现有功能不变
2. **向后兼容**
   - 尽可能保持 API 兼容
   - 如需破坏性变更，先询问用户
3. **有测试保护**
   - 修改前确保有测试覆盖
   - 新增功能必须写测试

## 执行步骤

### 第一步：阅读和理解 DDD 设计文档 📚

1. **检查文档存在**
   - 列出 `docs/ddd-models/` 目录下所有文件
   - 确认总览文档和各限界上下文文档存在
2. **阅读变更日志**
   - 了解文档版本历史
   - 识别本次变更的内容
3. **阅读总览文档**
   - 理解项目的整体架构
   - 识别所有限界上下文
   - 明确上下文之间的关系
   - **重点阅读「待实现功能清单」章节**，这是全局任务清单
4. **阅读各限界上下文文档**
   - 详细了解每个上下文的领域模型
   - 识别所有聚合根、实体、值对象
   - 理解领域服务和仓储接口定义
   - **重点阅读「待实现接口清单」章节**
   - 注意 `[NEW]`、`[UPDATED]`、`[DEPRECATED]` 标记

### 第二步：检查现有代码库 🔍

1. **扫描代码结构**
   - 列出 `src/main/java/` 和 `src/test/groovy/` 的包结构
   - 识别已存在的类
2. **对比设计与实现**
   - 创建对比表格：
     | 设计概念 | 是否已实现 | 状态 |
     |---------|----------|------|
     | {聚合根} | {是/否} | {NEW/UPDATED/EXISTING} |
3. **识别影响范围**
   - 确定哪些模块需要变更
   - 评估变更的风险

### 第三步：全局任务拆解（高纬度）📋

1. **从总览文档中提取「待实现功能清单」**
   - 按优先级排序
   - 明确每个功能点涉及的层级（interface、application、domain、infrastructure）

2. **生成全局任务清单**
   - 格式示例：
   ```markdown
   ## 全局任务清单
   
   ### 优先级1：订单限界上下文
   1. **创建订单**
      - 描述：用户创建新订单
      - 涉及层级：interface层、application层、domain层、infrastructure层
   
   2. **查询订单详情**
      - 描述：根据订单ID查询订单详情
      - 涉及层级：interface层、application层、domain层、infrastructure层
   ```

3. **确认任务清单**
   - 向用户展示任务清单
   - 确认优先级和范围
   - 确认是否需要调整

### 第四步：检查项目是否需要初始化 ⚙️

**在开始开发前，必须检查项目状态：**

1. **检查项目是否需要初始化**
   - 检查是否存在 pom.xml
   - 检查 src/main/java/ 和 src/test/groovy/ 目录是否存在
   - 检查主启动类是否存在

2. **如果需要初始化（项目代码不存在）**
   - **执行前置步骤：项目初始化**（按 08-development-workflow.md 中的前置步骤）
   - 创建 pom.xml、application.yml、.gitignore、主启动类
   - 创建 `arch-unit-constraints-test.groovy`（根据 01-architecture-constraints.md）
   - 验证 mvn compile 和 mvn test-compile 成功

3. **如果项目已存在，跳过此步，继续下一步**

### 第五步：循环执行每个功能点（按优先级）⚙️

对第三步拆解出的每个功能点，**循环执行**以下7阶段TDD流程：

#### 阶段1：单个功能点的详细任务拆解（低纬度）
- **从对应限界上下文文档的「待实现接口清单」中获取接口详情**
- **详细列出需要创建/修改的所有文件**，包括：
  - Domain层：值对象、聚合根、领域服务、仓储接口
  - Infrastructure层：PO、Mapper、RepositoryImpl、MapStruct转换器
  - Application层：Command、BO、ApplicationService、MapStruct转换器
  - Interface层：RequestDTO、ResponseDTO、Controller、MapStruct转换器
- **确定实现顺序**：Domain → Infrastructure → Application → Interface
- **向用户展示详细拆解计划并确认**

#### 阶段2：编写失败的测试（红灯）🔴
- 增量开发时，优先修改现有测试，再新增测试
- 按测试规范编写测试
- 运行测试，确保测试失败（红灯）

#### 阶段3：最小实现（绿灯）🟢
- 优先使用现有类，避免重复创建
- **必须实现完整四层**，不只是Domain层
- **必须运行 mvn test**，如果失败必须解决问题，禁止跳过
- **解决问题策略**：
  - 仔细阅读错误日志
  - 检查是否违反架构约束
  - 检查代码逻辑
  - 必要时回退

#### 阶段4：重构与审查（清洁）🧹
- 只重构本次变更的代码，不要重构无关部分
- **必须运行 mvn test**，如果失败必须解决问题，禁止跳过

#### 阶段5：端到端验证 ✅
- 运行现有回归测试，确保没有破坏
- **必须确保**：编译成功、启动成功、测试通过、接口正常

#### 阶段6：文档同步与完成 📝
- 更新相关文档（如有需要）
- 总结本功能点的完成情况

**当前功能点完成后，继续下一个功能点，直到所有功能点完成。**

### 第六步：总结与交付

所有功能点完成后：
1. 列出所有修改/新增的文件
2. 运行完整测试套件，确保所有测试通过
3. 向用户展示完成的功能清单

### 第七步：整体收尾与持续改进 🔄

在所有功能点完成后，从整体角度进行检查和改进：

1. **调用 code-review-and-improve Skill** 来自动化完成此阶段
   - 规范与Skill检查
   - 业务与架构检查
   - 代码质量检查
   - 测试覆盖检查
2. **必要时完善规范和Skill**
3. **提交所有变更**（如有未提交的代码）
4. **创建PR**（根据需要）

## 开发顺序建议

按照以下优先级进行开发（单个功能点内部）：

1. **Domain 层优先**
   - 值对象（无依赖）
   - 实体和聚合根
   - 领域服务
   - 仓储接口

2. **Infrastructure 层**
   - PO 对象
   - Mapper 接口
   - Repository 实现
   - MapStruct 转换器

3. **Application 层**
   - Command 对象
   - BO 对象
   - Application Service
   - MapStruct 转换器

4. **Interface 层**
   - Request/Response DTO
   - Controller
   - MapStruct 转换器

## 项目配置

- DDD设计文档目录：`docs/ddd-models/`
- 默认项目包名：`com.example.demoai`
