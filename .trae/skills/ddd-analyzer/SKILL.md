---
name: "ddd-analyzer"
description: "根据需求描述进行DDD领域建模，严格遵循10-ddd-modelling-process.md流程，识别子域、限界上下文、聚合根、实体等概念，生成结构化的DDD设计文档。支持增量更新，避免重复覆盖。"
---

# DDD分析 Skill

## 功能概述

根据需求描述进行DDD领域建模，识别子域、限界上下文、聚合根、实体等概念，生成结构化的DDD设计文档。支持增量更新，避免重复覆盖。

## 使用时机

当用户提及以下关键词时使用：
- DDD分析、DDD建模、领域建模
- 识别聚合根、分析业务概念
- 设计限界上下文、更新DDD设计

## 前置依赖

- [01-architecture-constraints.md](../../rules/01-architecture-constraints.md)
- [10-ddd-document-format.md](../../rules/10-ddd-document-format.md)
- [11-ddd-modelling-process.md](../../rules/11-ddd-modelling-process.md)

## 执行步骤

### 1. 现状分析与需求理解

- 检查 `docs/ddd-models/` 现有文档
- 深入理解业务目标和用户需求
- 提取关键业务概念
- 建立初步业务术语表

### 2. 战略架构设计

- 识别子领域（核心域、支撑域、通用域）
- 识别限界上下文
- 设计上下文映射

### 3. 战术设计

- 提取领域概念（名词、动词、业务规则）
- 识别聚合边界（遵循聚合设计4条铁律）
- 设计领域模型（聚合根、实体、值对象、领域服务、仓储接口）
- 梳理关系

### 4. 功能点与接口识别

- 识别待实现功能点（按优先级P0>P1>P2）
- 识别待实现RESTful接口
- 设计DTO/Command/BO详细定义

### 5. 文档生成与更新

- 按规范生成总览文档和各限界上下文文档
- 支持增量更新，标记[NEW]/[UPDATED]/[DEPRECATED]
- 添加变更日志

### 6. 需求验证

- 系统性比对文档与需求
- 识别遗漏并补充
- 最多循环3次验证
- 询问是否需要执行ddd-developer Skill进行代码生成

## 输出规范

文档结构：
```
docs/ddd-models/
├── {项目名}-DDD-Overview.md  # 总览文档
└── {上下文名}-Context.md     # 各限界上下文文档
```

## 项目配置

- 默认项目名称：`ai-demo`
- 文档根目录：`docs/ddd-models/`
