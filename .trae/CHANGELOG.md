
# 配置变更日志

本文件记录对 `.trae/` 目录下文件的所有变更：规则、skill、配置等。

---

## 2026-05-10 (最新)

### 修改时间
2026-05-10

### 修改原因
改进 changelog-recorder，增加醒目的提醒，确保每次修改 .trae/ 后都记得调用此 skill。

### 修改内容
- [skills/changelog-recorder/SKILL.md](skills/changelog-recorder/SKILL.md) - 更新
  - 更新：description，增加 ⚠️ 重要提醒
  - 新增：开头的"⚠️ 重要提醒"部分，强调每次修改后必须调用
  - 更新：使用时机，强调"立即调用"

---

## 2026-05-10 (之前最新)

### 修改时间
2026-05-10

### 修改原因
1. 完善 DDD 开发流程，创建统一的文档格式规范
2. 改进 ddd-analyzer 和 ddd-developer 工作流，提升执行效率和文档完整性
3. 调整阶段7的位置，将其作为整体收尾步骤而非单个子任务的一部分
4. 增加需求验证循环机制，确保文档完整性，同时加入循环保护

### 修改内容
- [rules/00-README.md](rules/00-README.md) - 更新
  - 新增：09-ddd-document-format.md 到规则列表
  - 更新：目录结构

- [rules/08-development-workflow.md](rules/08-development-workflow.md) - 更新
  - 调整：将阶段7移至后面，改为"整体收尾与持续改进"
  - 新增：适用场景说明
  - 更新：阶段编号

- [rules/09-ddd-document-format.md](rules/09-ddd-document-format.md) - 新增
  - 初始版本：DDD 设计文档格式规范
  - 包含：总览文档格式、限界上下文文档格式、领域模型详细设计格式
  - 包含：待实现功能清单、待实现接口清单要求
  - 包含：红线规则

- [skills/ddd-analyzer/SKILL.md](skills/ddd-analyzer/SKILL.md) - 更新
  - 新增：前置依赖规范，引用 09-ddd-document-format.md
  - 删除：重复的文档模板内容
  - 新增：第八步"需求验证与补充"，包含循环机制
  - 新增：循环限制（最多3次）
  - 新增：验证检查清单
  - 新增：超过限制时的输出模板
  - 更新：子部分编号（8.1、8.2、8.3）
  - 更新：执行流程

- [skills/ddd-developer/SKILL.md](skills/ddd-developer/SKILL.md) - 更新
  - 删除：任务记录相关内容
  - 调整：全局任务拆解（高纬度）和单个功能点详细拆解（低纬度）的区别
  - 强调：完整四层实现（Domain→Infrastructure→Application→Interface）
  - 调整：执行流程，按功能点循环执行7阶段TDD
  - 删除：子任务中的阶段7
  - 新增：第七步"整体收尾与持续改进"，所有功能点完成后调用
  - 明确：调用 code-review-and-improve 的时机

---

## 2026-05-10 (之前最新)

### 修改时间
2026-05-10

### 修改原因
完善 pom.xml 配置规范，确保项目能够正确编译、运行和测试。原来的描述过于简单，缺少构建插件、编译配置、测试配置等关键信息。

### 修改内容
- [rules/03-spring-boot-guide.md](rules/03-spring-boot-guide.md) - 更新
  - 新增：完整的 pom.xml 配置模板章节
  - 新增：配置项详细说明表格
  - 新增：关键依赖版本说明表格
  - 新增：测试文件命名约定说明

- [rules/08-development-workflow.md](rules/08-development-workflow.md) - 更新
  - 更新：第 20 行，将"创建 pom.xml（包含所有必需依赖...）"改为"创建 pom.xml（使用 03-spring-boot-guide.md 中的完整配置模板）"
  - 目的：引用完整的配置模板，确保 pom.xml 包含所有必需的构建插件和配置

---

## 2026-05-10 (最新-之前)

### 修改时间
2026-05-10

### 修改原因
1. 调整开发流程结构，将"项目初始化"从阶段0改为"前置步骤（可选）"
2. 将"8阶段TDD"优化为"7阶段核心TDD"，更清晰：
   - 前置步骤：项目初始化（可选）
   - 核心流程：阶段1-7
3. 同步更新 ddd-developer skill，确保与规则文件一致

### 修改内容
- [rules/08-development-workflow.md](rules/08-development-workflow.md) - 更新
  - 标题：从"8阶段TDD"改为"7阶段TDD"
  - 调整：将"阶段0：项目初始化"改为"前置步骤：项目初始化（可选）"
  - 新增："7阶段核心工作流程"标题，明确核心流程

- [skills/ddd-developer/SKILL.md](skills/ddd-developer/SKILL.md) - 更新
  - 标题：从"8阶段TDD"改为"7阶段TDD"
  - 调整：将"阶段0"描述改为"前置步骤"
  - 调整：将"按8阶段TDD流程开发"改为"按7阶段TDD流程开发"
  - 修正：将"第五步：变更影响记录"改为"第六步"

---

## 2026-05-10 (之前)

### 修改时间
2026-05-10

### 修改原因
1. 增加代码生成符号规范，严令禁止使用 HTML/XML 实体编码符号（如 `&lt;`、`&gt;`），必须直接使用原始符号（如 `<`、`>`）
2. 完善TDD开发流程，从7阶段扩展为8阶段：
   - 新增阶段0：项目初始化（快速搭建项目，创建arch-unit约束测试）
   - 强化阶段3、4：测试失败必须解决，禁止跳过
   - 强化阶段5：确保编译成功、启动成功、测试通过、接口正常
   - 更新红线规则
3. 更新ddd-developer skill，支持8阶段流程，包含阶段0和阶段7

### 修改内容
- [rules/02-java-coding-style.md](rules/02-java-coding-style.md) - 更新
  - 新增：第 7 章 "代码生成规范 —— 正确书写符号"
  - 新增：7.1 禁止使用 HTML/XML 实体编码符号，包含符号对照表和错误/正确示例
  - 新增：7.2 适用场景说明
  - 更新：将原第 7 章调整为第 8 章
  - 更新：自动化检查清单，新增符号检查项

- [rules/08-development-workflow.md](rules/08-development-workflow.md) - 更新
  - 标题：从"7阶段TDD"改为"8阶段TDD"
  - 新增：阶段0"项目初始化"，包含完整的执行步骤和检查清单
  - 更新：阶段3、4，强化测试失败处理要求
  - 更新：阶段5，强化端到端验证（编译、启动、测试、接口）
  - 更新：红线规则，新增规则6、7

- [skills/ddd-developer/SKILL.md](skills/ddd-developer/SKILL.md) - 更新
  - 标题：从"6阶段TDD"改为"8阶段TDD"
  - 新增：第四步"检查项目是否需要初始化"
  - 更新：第五步"按8阶段TDD流程开发"，包含阶段0-7
  - 更新：进度记录规范，新增00-项目初始化.md和07-改进回顾.md模板
  - 更新：所有相关描述

---

## 2026-05-10 (之前)

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

