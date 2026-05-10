
# 配置变更日志

本文件记录对 `.trae/` 目录下文件的所有变更：规则、skill、配置等。

---

## 2026-05-10 (最新)

### 修改时间
2026-05-10

### 修改原因
增加TDD流程的第7阶段：持续改进与回顾。创建新Skill code-review-and-improve来实现此环节，使流程更完整，支持规范和Skill的持续进化。

### 修改内容
- [rules/08-development-workflow.md](rules/08-development-workflow.md) - 更新
  - 标题：从"6阶段TDD"改为"7阶段TDD"
  - 新增：阶段7"持续改进与回顾"，包含完整的执行步骤和检查清单

- [skills/code-review-and-improve/SKILL.md](skills/code-review-and-improve/SKILL.md) - 新增
  - 初始版本：代码审查与持续改进Skill
  - 实现：规范与Skill检查、业务与架构检查、代码质量检查、测试覆盖检查
  - 实现：规范与Skill进化、Git操作与PR创建
  - 包含：检查报告模板

- [config.yml](config.yml) - 更新
  - 新增：在 harness-engineer 的 skills 列表中添加 "code-review-and-improve"

---

## 2026-05-10 (之前1)

### 修改时间
2026-05-10

### 修改原因
优化 ddd-developer/SKILL.md，消除与 08-development-workflow.md 的重复内容，明确职责分离：规则文件是"规范之源"，Skill文件是"执行指南"。

### 修改内容
- [skills/ddd-developer/SKILL.md](skills/ddd-developer/SKILL.md) - 更新
  - 新增："前置依赖规范"章节，明确引用所有规则文件
  - 新增："相比基础规范的增强"章节，突出Skill的增量价值
  - 简化：6阶段TDD描述，改为引用 08-development-workflow.md
  - 删除：重复的约束检查部分，统一引用规则文件

---

## 2026-05-10 (之前)

### 修改时间
2026-05-10

### 修改原因
1. 解决多会话中遗忘记录变更的问题，创建专门的变更记录 skill
2. 修正错误理解：ddd-analyzer/ddd-developer 输出的是项目代码/文档，不需要记录到 .trae/CHANGELOG.md
3. 在配置中启用 changelog-recorder skill
4. 配置 GitHub MCP 支持，提供配置示例和环境变量模板

### 修改内容
- [skills/changelog-recorder/SKILL.md](skills/changelog-recorder/SKILL.md) - 新增
  - 初始版本，用于记录 `.trae/` 目录下的所有变更
  - 支持自动检测 Git 变更
  - 提供标准化的记录格式
  - 包含变更类型定义

- [skills/ddd-analyzer/SKILL.md](skills/ddd-analyzer/SKILL.md) - 更新
  - 删除：移除错误添加的 changelog-recorder 调用提醒（该 skill 修改的是 docs/）

- [skills/ddd-developer/SKILL.md](skills/ddd-developer/SKILL.md) - 更新
  - 删除：移除错误添加的 changelog-recorder 调用提醒（该 skill 修改的是 src/ 和 docs/）

- [config.yml](config.yml) - 更新
  - 新增：在 harness-engineer 的 skills 列表中添加 "changelog-recorder"

- [mcp-config.example.yml](mcp-config.example.yml) - 新增
  - GitHub MCP 配置示例文件
  - 包含常见 MCP 服务器配置模板

- [env.example](env.example) - 新增
  - 环境变量配置示例
  - 包含 GitHub Token 配置说明

- [.gitignore](.gitignore) - 新增
  - 防止敏感文件提交到 Git

---

## 2026-05-10

### 修改时间
2026-05-10

### 修改原因
支持需求变更场景，避免重复生成文档和重复创建类。

### 修改内容
- [skills/ddd-analyzer/SKILL.md](skills/ddd-analyzer/SKILL.md) - 更新
  - 新增：增量更新策略
  - 新增：文档检查步骤
  - 新增：差异分析流程
  - 新增：变更标记 ([NEW]/[UPDATED]/[DEPRECATED])
  - 新增：变更日志模板
  - 新增：备份目录结构 (archive/)
  - 更新：描述信息，说明支持增量更新

- [skills/ddd-developer/SKILL.md](skills/ddd-developer/SKILL.md) - 更新
  - 新增：增量开发策略
  - 新增：代码库现状检查步骤
  - 新增：差异分析与任务制定
  - 新增：最小改动原则
  - 新增：变更影响记录
  - 更新：任务目录命名，增加操作类型标识
  - 更新：所有记录模板，增加变更类型区分
  - 更新：描述信息，说明支持增量开发

- [config.yml](config.yml) - 清理
  - 删除：不存在的 skill 引用 (code-generator, ddd-modeler)

---

## 2026-05-10 (早)

### 修改时间
2026-05-10 上午

### 修改原因
完善 DDD 开发流程，创建分析和开发两个 Skill。

### 修改内容
- [skills/ddd-analyzer/SKILL.md](skills/ddd-analyzer/SKILL.md) - 新增
  - 初始版本，DDD 分析建模功能

- [skills/ddd-developer/SKILL.md](skills/ddd-developer/SKILL.md) - 新增
  - 初始版本，根据 DDD 文档开发功能

- [config.yml](config.yml) - 更新
  - 添加新 skill 引用

---

## 2026-05-10 (更早)

### 修改时间
2026-05-10

### 修改原因
优化规则管理，采用方案 A 合并简化层次。

### 修改内容
- [rules/03-spring-boot-guide.md](rules/03-spring-boot-guide.md) - 新增
- [rules/04-mybatis-plus-guide.md](rules/04-mybatis-plus-guide.md) - 新增
- [rules/05-mapstruct-guide.md](rules/05-mapstruct-guide.md) - 新增
- [rules/06-spock-testing-guide.md](rules/06-spock-testing-guide.md) - 新增
- [rules/00-README.md](rules/00-README.md) - 更新
- 多个旧规则文件 - 删除
- [rules/technology/](rules/technology/) - 删除

---

## 记录规范

每次对 `.trae/` 下文件变更时，请按以下格式记录：

```markdown
## YYYY-MM-DD

### 修改时间
具体时间（可选）

### 修改原因
简单描述变更原因

### 修改内容
- [文件路径](相对路径) - {变更类型：新增/更新/删除/重命名}
  - 变更详情...
  - 变更详情...
```

---

## 变更类型说明

| 类型 | 说明 |
|-----|------|
| 新增 | 创建新文件 |
| 更新 | 修改现有文件内容 |
| 删除 | 删除文件 |
| 重命名 | 文件改名 |
| 清理 | 移除冗余内容 |

