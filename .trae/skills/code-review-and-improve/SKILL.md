---
name: "code-review-and-improve"
description: "代码审查与持续改进：检查规范和Skill、业务功能、代码质量、测试覆盖，完善规范和Skill，提交代码并创建PR。"
---

# 代码审查与持续改进 Skill

## 功能概述

执行代码审查与持续改进，检查规范和Skill、业务功能、代码质量、测试覆盖，完善规范和Skill，提交代码并创建PR。

## 使用时机

- 完成ddd-developer后自动触发
- 独立进行代码审查和流程改进
- 用户明确要求进行持续改进

## 前置依赖

- [01-architecture-constraints.md](../../rules/01-architecture-constraints.md)
- [02-java-coding-style.md](../../rules/02-java-coding-style.md)
- [07-git-commit-message.md](../../rules/07-git-commit-message.md)

## 执行步骤

### 1. 规范与Skill检查

- 阅读 `.trae/rules/` 所有规则文件
- 阅读 `.trae/skills/` 所有Skill
- 检查冗余、边界不清、描述不明问题
- 记录问题清单

### 2. 业务与架构检查

- 阅读 `docs/ddd-models/` DDD文档
- 检查代码是否按DDD分层实现
- 验证架构合规性

### 3. 代码质量检查

- 对照编码规范检查
- 记录违反规范的代码

### 4. 测试覆盖检查

- 检查测试文件命名规范
- 验证测试覆盖正常/失败/边界路径
- 记录缺失的测试

### 5. 规范与Skill进化

- 根据问题制定修改计划
- 执行修改
- 记录变更

### 6. Git操作与PR

- 检查Git状态
- 按规范提交变更
- 创建feature branch
- 推送并创建PR

## 输出规范

生成 `docs/code-review-report.md`，包含：
- 规范与Skill检查结果
- 业务与架构检查结果
- 代码质量检查结果
- 测试覆盖检查结果
- Git与PR信息

## 项目配置

- 检查报告目录：`docs/`
- 规范目录：`.trae/rules/`
- Skill目录：`.trae/skills/`
- DDD文档目录：`docs/ddd-models/`
- 源代码目录：`src/`
