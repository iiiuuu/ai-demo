---
name: "ddd-developer"
description: "根据 ddd-analyzer 生成的 DDD 设计文档进行代码开发，严格遵循 7阶段TDD 开发流程。支持增量开发，避免重复创建已有类。"
---

# DDD开发者 Skill

## 功能概述

根据 ddd-analyzer 生成的 DDD 设计文档进行代码开发，严格遵循 7阶段TDD 开发流程。支持增量开发，避免重复创建已有类。

## 使用时机

- 用户明确要求根据 DDD 设计文档进行代码实现
- `docs/ddd-models/` 目录下存在设计文档且用户需要实现
- 在 ddd-analyzer 执行成功后，用户继续要求代码实现

## 前置依赖

- [01-architecture-constraints.md](../../rules/01-architecture-constraints.md)
- [02-java-coding-style.md](../../rules/02-java-coding-style.md)
- [03-spring-boot-guide.md](../../rules/03-spring-boot-guide.md)
- [04-mybatis-plus-guide.md](../../rules/04-mybatis-plus-guide.md)
- [05-mapstruct-guide.md](../../rules/05-mapstruct-guide.md)
- [06-spock-testing-guide.md](../../rules/06-spock-testing-guide.md)
- [08-ddd-developer.md](../../rules/08-ddd-developer.md)
- [09-tdd-constraints.md](../../rules/09-tdd-constraints.md)

## 执行步骤

### 1. 阅读和理解 DDD 设计文档

- 检查 `docs/ddd-models/` 文档存在
- 阅读总览文档和各限界上下文文档
- 重点关注「待实现功能清单」和「待实现接口清单」

### 2. 检查现有代码库

- 扫描 `src/main/java/` 和 `src/test/groovy/` 包结构
- 对比设计与实现，识别已存在的类
- 确定影响范围

### 3. 全局任务拆解

- 从总览文档提取「待实现功能清单」
- 按优先级排序
- 生成全局任务清单并向用户确认

### 4. 项目初始化（如需要）

- 检查项目是否需要初始化
- 如需要，创建 pom.xml、application.yml、主启动类等
- 验证 mvn compile 成功

### 5. 循环执行每个功能点

- 按优先级循环执行
- 调用 tdd-7-steps skill 执行单个功能点
- 开发顺序：Domain → Infrastructure → Application → Interface
- 每个功能点完成后检查剩余任务，询问用户是否继续

### 6. 总结与交付

- 列出所有修改/新增的文件
- 运行完整测试套件
- 向用户展示完成的功能清单

### 7. 持续改进

- 调用 code-review-and-improve skill
- 完善规范和Skill
- 提交变更并创建PR

## 输出规范

- 按DDD四层架构完整实现代码
- 包含完整的测试用例
- 遵循所有编码规范

## 项目配置

- DDD设计文档目录：`docs/ddd-models/`
- 默认项目包名：`com.example.demoai`
