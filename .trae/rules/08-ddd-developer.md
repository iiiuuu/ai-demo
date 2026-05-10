
# DDD开发者约束

&gt; **AI Agent在使用 ddd-developer skill 时必须严格遵守！**

## 📋 前置步骤：项目初始化 ⚙️（可选）
**目标**：快速搭建项目基础结构，确保可编译、可运行

**适用场景**：
- 项目代码不存在（src/ 目录为空或不存在）
- pom.xml 不存在
- 主启动类不存在

**执行步骤**：
1. **检查项目状态**
   - 检查是否存在 pom.xml
   - 检查 src/ 目录结构
   - 检查 .gitignore 是否存在

2. **创建项目基础配置**
   - 创建 pom.xml（使用 03-spring-boot-guide.md 中的完整配置模板）
   - 创建 application.yml（H2数据库配置、MyBatis-Plus配置）
   - 创建 .gitignore（标准Java项目配置）
   - 创建主启动类（@SpringBootApplication）

3. **创建架构约束测试**
   - 创建 `arch-unit-constraints-test.groovy`（根据 01-architecture-constraints.md 的内容）
   - 测试必须覆盖：
     - Domain层禁止导入MapStruct、MyBatis-Plus、Spring Web
     - Interface层只能依赖Application层
     - Application层只能依赖Domain层
     - Infrastructure层只能依赖Domain层
     - 各层对象转换必须通过MapStruct
   - **重要**：此时此测试应该失败（因为还没有代码），但必须确保测试代码本身可以编译通过

4. **验证项目初始化成功**
   - 运行 `mvn compile` 确保编译成功
   - 运行 `mvn test-compile` 确保测试代码编译成功
   - 确保没有错误

5. **提交变更**
   - 提交信息格式：`INIT: [项目初始化描述]`
   - Git commit 包含所有初始化文件

**检查清单**：
- [ ] pom.xml 已创建，包含所有必需依赖
- [ ] application.yml 已创建
- [ ] 主启动类已创建
- [ ] arch-unit-constraints-test.groovy 已创建
- [ ] mvn compile 成功
- [ ] mvn test-compile 成功
- [ ] Git commit 已提交

---

## 🚨 DDD开发者红线规则（绝对不能违反）

| # | 规则 | 后果 |
|---|------|------|
| 1 | 严格按优先级顺序执行：P0 &gt; P1 &gt; P2，不得跳跃 | 任务暂停，重新按优先级排序执行 |
| 2 | 多个功能点时，完成一个后必须检查剩余任务并询问是否继续 | 视为流程违规，必须重新执行完整流程 |
| 3 | 所有功能点完成后，必须执行总结与交付和整体收尾与持续改进 | 流程不完整，必须补充执行 |
| 4 | 不能自创架构规则 | 视为架构漂移，任务暂停 |
| 5 | 提交的代码必须通过ArchUnit测试 | 根据失败日志修正分层违规 |

---

## 🔄 多功能点循环执行约束（ddd-developer 专用）

当存在多个待实现功能点时，**必须严格遵守以下流程**：

### 1. 优先级排序机制
- 从总览文档的「待实现功能清单」中提取所有功能点
- 按优先级分组：P0（最高） &gt; P1 &gt; P2（最低）
- 同一优先级内按文档中的排列顺序执行
- 每个功能点必须包含「参考文档」字段，指向对应的限界上下文文档

### 2. 循环执行逻辑
对每个功能点执行完整的7步TDD流程（**调用 tdd-7-steps skill**）：
1. 阶段1：单个功能点的详细任务拆解
2. 阶段2：编写失败的测试（红灯）
3. 阶段3：最小实现（绿灯）
4. 阶段4：重构与审查（清洁）
5. 阶段5：端到端验证
6. 阶段6：文档同步与完成
7. **阶段7：检查是否继续下一个任务 ⭐（必须执行）**

### 3. 阶段7的强制要求（当前功能点完成后）
**必须按顺序执行以下5个步骤，不得省略**：
1. **更新 TodoWrite 任务列表**：将当前功能点标记为「已完成」
2. **检查剩余任务**：扫描任务列表，确认是否还有未完成的高优先级功能点
3. **向用户展示进度并询问**：
   - 展示已完成的功能点清单
   - 展示剩余的功能点清单（按优先级排序）
   - 明确询问：「是否继续执行下一个功能点？」
4. **如果用户确认继续**：自动进入下一个功能点的「阶段1」
5. **如果用户选择暂停**：清晰总结已完成的工作和待完成的任务

### 4. 循环终止条件
循环执行在以下任一情况发生时终止：
- ✅ **所有功能点完成**：自动进入总结与交付和整体收尾与持续改进
- ⏸️ **用户选择暂停**：清晰总结当前状态，等待用户指示
- ❌ **遇到不可恢复的错误**：清晰报告错误，等待用户指示

### 5. 绝对禁止行为
- ❌ 禁止在完成第一个功能点后自动停止
- ❌ 禁止跳过阶段7的检查和询问
- ❌ 禁止跳过高优先级任务去执行低优先级任务
- ❌ 禁止在所有功能点完成后跳过总结与交付和整体收尾

---

## 📊 进度跟踪模板

在任务分析阶段，使用此模板：
```markdown
# {任务名称} 作战计划

## 子任务清单
- [ ] 子任务1：实现核心领域逻辑
  - [ ] 新建Order聚合根
  - [ ] 新建OrderRepository接口
  - [ ] 编写OrderSpec测试
  - [ ] 关键测试：库存不足时抛出InsufficientStockException

- [ ] 子任务2：实现应用服务编排
  - [ ] 新建CreateOrderCommand
  - [ ] 新建OrderApplicationService
  - [ ] 编写OrderApplicationServiceSpec测试

## 风险与依赖
- [ ] 依赖：User领域服务
- [ ] 风险：库存服务可能超时
```

---

## 📚 相关资源

- **Skills**：
  - [tdd-7-steps](../skills/tdd-7-steps/SKILL.md) - TDD 7步流程独立技能（单个功能点实施）
  - [ddd-developer](../skills/ddd-developer/SKILL.md) - DDD开发者技能（多功能点管理）
  - [code-review-and-improve](../skills/code-review-and-improve/SKILL.md) - 代码审查与持续改进（整体收尾）
  - [changelog-recorder](../skills/changelog-recorder/SKILL.md) - 变更记录器
