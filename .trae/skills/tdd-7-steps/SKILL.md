---
name: "tdd-7-steps"
description: "TDD（测试驱动开发）7步流程独立技能模块，包含完整的实施步骤、适用场景、注意事项和最佳实践。可在不同项目和开发场景中复用。"
---

# TDD（测试驱动开发）7步流程 Skill

## 功能概述

封装完整的TDD（测试驱动开发）7步流程方法论，提供标准化的开发流程指导。可被独立引用和调用，便于在不同项目和开发场景中复用。

## 使用时机

- 功能开发：按TDD方式实现新功能
- Bug修复：先写失败测试再修复
- 重构：重构前补充测试，确保行为不变
- 技能学习：学习和实践TDD方法论
- 集成使用：作为其他skill的子流程

## 前置依赖

- [06-spock-testing-guide.md](../../rules/06-spock-testing-guide.md)
- [09-tdd-constraints.md](../../rules/09-tdd-constraints.md)

## 执行步骤

### 1. 任务分析与规划

- 理解需求，明确任务边界
- 识别涉及的领域概念和技术分层
- 列出需要创建/修改的所有对象
- 将大任务拆分为独立子任务

### 2. 编写失败的测试（红灯）

- 为**所有层**的类创建对应的测试文件（`*Spec.groovy`）：
  - **Domain层**：聚合根、实体、值对象、领域服务
  - **Infrastructure层**：Repository实现、MapStruct转换器
  - **Application层**：ApplicationService、MapStruct转换器
  - **Interface层**：Controller、MapStruct转换器
- 使用对应的测试模板：
  - [领域测试模板](../../rules/templates/testing-guide-template-domain-entity.md)
  - [应用服务测试模板](../../rules/templates/testing-guide-template-application-service.md)
  - [MapStruct测试模板](../../rules/templates/testing-guide-template-mapstruct.md)
  - [Repository集成测试模板](../../rules/templates/testing-guide-template-repository-it.md)
  - [Controller集成测试模板](../../rules/templates/testing-guide-template-controller-it.md)
- 测试覆盖正常路径、失败路径、边界条件
- 此时不创建任何实现代码
- 运行测试，确保测试失败
- 提交变更（仅包含测试文件）

### 3. 最小实现（绿灯）

- 根据测试编写最小实现代码
- 不添加额外功能
- 运行测试，确保测试通过
- 提交变更

### 4. 重构与审查（清洁）

- 在测试保护下进行重构
- 优化方向：消除重复、改善命名、优化结构
- 运行测试，确保重构没有破坏功能
- 提交变更

### 5. 端到端验证

- 编译验证：确保项目编译成功
- 测试验证：运行所有单元测试和集成测试
- 运行验证：启动应用，确保正常启动
- 接口验证：验证API端点（如适用）
- 数据库验证：确保数据操作正确（如适用）

### 6. 文档同步与完成

- 检查是否需要更新文档
- 列出完成的子任务
- 说明已知限制
- 宣布任务完成

### 7. 持续改进与总结

- 回顾开发过程
- 识别可以改进的地方
- 检查是否需要完善规范或技能

## 输出规范

- 完整的测试用例覆盖
- 符合编码规范的实现代码
- 清晰的Git提交历史

## 项目配置

- 测试目录：`src/test/groovy/`
- 源代码目录：`src/main/java/`
