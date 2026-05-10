---
name: "code-review-and-improve"
description: "代码审查与持续改进：检查规范和Skill、业务功能、代码质量、测试覆盖，完善规范和Skill，提交代码并创建PR。对应08-development-workflow.md的阶段7。"
---

# 代码审查与持续改进 Skill

## 功能概述

本Skill用于执行08-development-workflow.md中的"阶段7：持续改进与回顾"，实现以下能力：
1. 检查规范和Skill是否存在冗余、边界不清晰、描述不明确
2. 检查业务功能和架构是否符合规范
3. 检查代码质量是否存在问题
4. 检查测试是否缺失
5. 完善规范和Skill，实现进化
6. 提交代码、新建branch、推送、创建PR

## 使用时机

在以下情况调用本Skill：
1. 完成ddd-developer的6阶段后，自动触发
2. 独立进行代码审查和流程改进时
3. 用户明确要求进行持续改进时

## 执行步骤

### 第一步：规范与Skill检查 🔍

1. **阅读所有规范**
   - 列出 `.trae/rules/` 目录下的所有规则文件
   - 阅读每个规则文件的内容
   - 检查是否存在：
     - 冗余内容（多个文件说同样的事）
     - 边界不清晰（职责重叠）
     - 描述不明确（无法执行）

2. **阅读所有Skill**
   - 列出 `.trae/skills/` 目录下的所有Skill
   - 阅读每个Skill的内容
   - 检查是否存在：
     - 与规范重复的内容
     - Skill之间边界不清晰
     - 描述不明确的步骤

3. **记录问题**
   - 创建检查报告，记录发现的所有问题
   - 分类：冗余、边界不清、描述不明

### 第二步：业务与架构检查 🏗️

1. **阅读DDD文档**
   - 检查 `docs/ddd-models/` 目录
   - 阅读总览文档和各限界上下文文档
   - 理解业务需求和架构设计

2. **检查已有代码**
   - 列出 `src/` 目录结构
   - 检查代码是否按照DDD分层实现
   - 对比设计文档和实际实现，检查是否一致

3. **架构合规性检查**
   - 检查是否违反01-architecture-constraints.md
   - 检查分层依赖是否正确
   - 检查是否存在架构漂移

### 第三步：代码质量检查 ✅

1. **根据规范检查**
   - 对照02-java-coding-style.md检查编码规范
   - 对照03-spring-boot-guide.md检查Spring Boot使用
   - 对照04-mybatis-plus-guide.md检查MyBatis-Plus使用
   - 对照05-mapstruct-guide.md检查MapStruct使用

2. **列出问题**
   - 记录所有违反规范的代码
   - 分类：命名问题、结构问题、使用问题

### 第四步：测试覆盖检查 🧪

1. **检查测试文件**
   - 列出 `src/test/` 目录下的所有测试文件
   - 检查测试文件命名是否符合规范（*Spec.groovy, *IT.groovy）

2. **对比实现代码**
   - 检查每个实现类是否有对应的测试
   - 检查测试是否覆盖：
     - 正常路径
     - 失败路径
     - 边界条件

3. **记录缺失的测试**
   - 列出所有缺失测试的类或方法
   - 建议需要补充的测试类型

### 第五步：规范与Skill进化 🔧

1. **根据发现的问题**
   - 决定需要修改哪些规范或Skill
   - 制定修改计划

2. **执行修改**
   - 修改对应的rule文件
   - 修改对应的skill文件
   - 确保修改后的内容清晰、无冗余、边界明确

3. **记录变更**
   - 使用changelog-recorder记录所有变更

### 第六步：Git操作与PR 🚀

1. **检查Git状态**
   - 查看当前有哪些变更
   - 区分：代码变更、文档变更、配置变更

2. **提交代码**
   - 按照规范的Git提交格式提交变更
   - 每个提交应该是单一主题

3. **新建Branch**
   - 创建新的feature branch
   - 命名规范：`feature/xxx` 或 `improve/xxx`

4. **推送代码**
   - 使用GitHub MCP推送branch到远程仓库

5. **创建PR**
   - 使用GitHub MCP创建Pull Request
   - PR描述包含：
     - 变更内容
     - 检查结果
     - 问题清单
     - 改进点

## 输出产物

### 检查报告

生成 `docs/code-review-report.md`，包含：
```markdown
# 代码审查报告

## 日期
YYYY-MM-DD

## 一、规范与Skill检查
### 发现的问题
- [ ] 问题1：描述
  - 位置：文件路径
  - 类型：冗余/边界不清/描述不明
- [ ] 问题2：描述

### 已完成的改进
- [x] 改进1：描述

## 二、业务与架构检查
### 符合规范
- [x] 检查项1
### 存在问题
- [ ] 问题1：描述

## 三、代码质量检查
### 发现的问题
- [ ] 问题1：描述
  - 文件：路径
  - 行号：xxx

## 四、测试覆盖检查
### 完整覆盖
- [x] 类1
### 缺失测试
- [ ] 类2：缺失测试类型

## 五、Git与PR
- Branch名称：xxx
- PR链接：xxx
```

## 前置条件

- 已完成08-development-workflow.md的阶段1-6
- 有GitHub MCP配置（mcp-config.example.yml）
- 有GitHub Token配置（env.example）

## 与其他Skill的协作

- 在ddd-developer完成后，可自动触发本Skill
- 使用changelog-recorder记录规范和Skill的变更
- 可与ddd-analyzer、ddd-developer配合使用

## 项目配置

- 检查报告输出目录：`docs/`
- 规范目录：`.trae/rules/`
- Skill目录：`.trae/skills/`
- DDD文档目录：`docs/ddd-models/`
- 源代码目录：`src/`
