
# 配置变更日志

本文件记录对 `.trae/` 目录下文件的所有变更：规则、skill、配置等。

---

## 2026-05-10 (最新)

### 修改时间
2026-05-10

### 修改原因
将原 08-development-workflow.md 拆分为两个更清晰的规则文件，分别针对 ddd-developer skill 和 tdd-7-steps skill，避免职责混杂。

### 修改内容
- **新增**：[rules/08-ddd-developer.md](rules/08-ddd-developer.md)
  - **包含**：前置初始化步骤、DDD开发者红线规则、多功能点循环执行约束、进度跟踪模板
  - **目标**：专为 ddd-developer skill 提供约束和规范
- **新增**：[rules/09-tdd-constraints.md](rules/09-tdd-constraints.md)
  - **包含**：TDD红线规则、7步流程阶段定义、提交信息格式规范
  - **目标**：专为 tdd-7-steps skill 提供约束和规范
- **删除**：原 [rules/08-development-workflow.md](rules/08-development-workflow.md)
  - 职责拆分为上述两个新文件
- **重命名**：调整后续规则文件编号
  - [rules/09-ddd-document-format.md](rules/10-ddd-document-format.md) → 09 → 10
  - [rules/10-ddd-modelling-process.md](rules/11-ddd-modelling-process.md) → 10 → 11
- **更新**：[rules/00-README.md](rules/00-README.md)
  - 更新快速开始列表和目录结构
- **更新**：[skills/ddd-developer/SKILL.md](skills/ddd-developer/SKILL.md)
  - 更新规则引用
- **更新**：[skills/tdd-7-steps/SKILL.md](skills/tdd-7-steps/SKILL.md)
  - 更新规则引用
- **更新**：[config.yml](config.yml)
  - 更新规则配置

---

---

## 2026-05-10 (之前最新)

### 修改时间
2026-05-10

### 修改原因
进一步优化职责划分，将TDD流程的具体实施完全交给 tdd-7-steps skill，rules 保留约束和规范，避免内容重复。

### 修改内容
- [rules/08-development-workflow.md](rules/08-development-workflow.md) - 重大调整
  - **简化**：单个功能点的7步TDD流程详细描述
  - **保留**：红线规则、多功能点循环执行约束、前置初始化步骤
  - **新增**：参考 tdd-7-steps skill 的指引
  - **新增**：相关资源章节，列出所有相关skill
- [skills/ddd-developer/SKILL.md](skills/ddd-developer/SKILL.md) - 更新
  - **简化**：第五步单个功能点的实施细节
  - **改为**：调用 tdd-7-steps skill 执行单个功能点
  - **保留**：多功能点循环执行、阶段7检查和询问的逻辑

---

## 2026-05-10 (之前最新)

### 修改时间
2026-05-10

### 修改原因
用户希望将TDD（测试驱动开发）的7步流程抽取为一个独立的技能模块，便于在不同项目和开发场景中复用。

### 修改内容
- [skills/tdd-7-steps/SKILL.md](skills/tdd-7-steps/SKILL.md) - 新增
  - **创建**：独立的TDD 7步流程技能模块
  - **包含**：完整的实施步骤、适用场景、注意事项、最佳实践
  - **支持**：独立引用和调用，便于与其他skill协同工作
- [skills/ddd-developer/SKILL.md](skills/ddd-developer/SKILL.md) - 更新
  - **新增**：相关Skills章节
  - **引用**：tdd-7-steps skill作为子流程

---

## 2026-05-10 (之前最新)

### 修改时间
2026-05-10

### 修改原因
用户需要将执行机制从口头承诺转变为硬性约束，确保 ddd-developer 严格按照循环执行流程工作。

### 修改内容
- [rules/08-development-workflow.md](rules/08-development-workflow.md) - 重大更新
  - **新增**：红线规则8-10，关于多功能点循环执行的强制约束
  - **新增**：完整的「多功能点循环执行约束」章节
  - **明确**：优先级排序机制、循环执行逻辑、阶段7强制要求、循环终止条件、绝对禁止行为

---

## 2026-05-10 (之前最新)

### 修改时间
2026-05-10

### 修改原因
用户反馈系统在使用 ddd-developer 时存在三个问题需要优化：
1. 任务执行不完整，仅完成第一个任务后就停止
2. 执行流程不完整，跳过了第六步和第七步
3. 待实现功能清单缺少参考文档信息

### 修改内容
- [rules/09-ddd-document-format.md](rules/09-ddd-document-format.md) - 更新
  - **新增**：在待实现功能清单模板中增加「参考文档」字段
  - **新增**：在要求中明确必须包含「参考文档」字段
- [skills/ddd-developer/SKILL.md](skills/ddd-developer/SKILL.md) - 更新
  - **新增**：阶段7「检查是否继续下一个任务」
  - **更新**：明确要求每个功能点完成后必须检查任务列表并询问用户是否继续
  - **更新**：强调严格按优先级循环执行流程

---

## 2026-05-10 (之前最新)

### 修改时间
2026-05-10

### 修改原因
用户指出 ddd-analyzer 在总览文档中提到了3个限界上下文，但只生成了2个的详细文档，缺少"支付"上下文。为了从根源上杜绝此问题，需要在规则中增加约束，确保步骤12的需求验证中必须检查限界上下文数量与文档数量的一致性。

### 修改内容
- [rules/10-ddd-modelling-process.md](rules/10-ddd-modelling-process.md) - 更新
  - **新增**：验收标准中增加两条关于限界上下文文档完整性的检查
  - **新增**：步骤12的强制性检查，包含限界上下文与文档一致性检查清单
  - **更新**：检查清单分为两部分，第一部分必须首先执行
- [skills/ddd-analyzer/SKILL.md](skills/ddd-analyzer/SKILL.md) - 更新
  - **更新**：步骤12的需求验证检查清单
  - **新增**：1. 限界上下文与文档一致性检查（必须首先执行）
  - **新增**：2. 需求完整性检查

---

## 2026-05-10 (之前最新)

### 修改时间
2026-05-10

### 修改原因
完善 DDD 设计文档格式，补充 DTO/Command/BO 的详细字段定义，让 ddd-developer 能直接据此写代码。

### 修改内容
- [09-ddd-document-format.md](../../rules/09-ddd-document-format.md) - 大幅更新
  - **新增**：DTO/Command/BO 格式规范，包含完整示例
  - **新增**：限界上下文文档第5章「DTO/Command/BO 详细定义」
  - **更新**：限界上下文文档章节结构，加入第5章
  - **更新**：红线规则，新增第3条和第7条
- [skills/ddd-analyzer/SKILL.md](skills/ddd-analyzer/SKILL.md) - 小幅更新
  - **更新**：步骤10，新增「设计DTO/Command/BO详细定义」部分
  - **更新**：产出物列表，加入「DTO/Command/BO详细定义」

---

## 2026-05-10 (之前最新)

### 修改时间
2026-05-10

### 修改原因
用户反馈 skill.md 中"完整建模流程"和"执行流程"存在冗余，建议融合为一个，让文档更清晰。

### 修改内容
- [skills/ddd-analyzer/SKILL.md](skills/ddd-analyzer/SKILL.md) - 重构
  - 合并："完整建模流程"和"执行流程"为一个统一的流程描述
  - 标题：从"完整建模流程（12步）"改为"完整建模流程（12步 + 总结）"
  - 新增：步骤 13"输出总结"，整合原执行流程中的输出总结部分
  - 新增：在步骤 11 中整合"增量更新策略"要点
  - 删除：独立的"执行流程"章节，避免冗余

---

## 2026-05-10 (之前最新)

### 修改时间
2026-05-10

### 修改原因
用户要求进行去重，将 10-ddd-modelling-process.md 和 skill.md 的内容分离：规则文件保留约束和原则，skill 文件保留执行流程，避免内容重复。

### 修改内容
- [rules/10-ddd-modelling-process.md](rules/10-ddd-modelling-process.md) - 重构
  - 更新：标题从"DDD建模流程规范"改为"DDD建模约束规范"
  - 删除：完整的 12 步建模流程（移至 skill.md）
  - 保留：核心思想、战略设计约束、战术设计约束、聚合设计铁律、验收标准
  - 保留：子领域划分、限界上下文划分原则、上下文映射模式
  - 保留：实体vs值对象对比、领域服务设计原则
  - 保留：领域模型组件要求

- [skills/ddd-analyzer/SKILL.md](skills/ddd-analyzer/SKILL.md) - 重构
  - 更新：description，强调遵循"约束规范"
  - 删除：重复的聚合设计铁律、实体vs值对象对比等内容
  - 新增：在各步骤中引用 10-ddd-modelling-process.md
  - 保留：完整的 12 步建模流程（详细执行步骤）
  - 保留：增量更新策略、文档格式规范、输出规范、执行流程

---

## 2026-05-10 (之前最新)

### 修改时间
2026-05-10

### 修改原因
用户要求完善DDD建模流程，基于ddd-crew的DDD建模流程和业界最佳实践，创建完整的DDD建模规则，并全面更新ddd-analyzer skill，使其遵循标准的12步建模流程。

### 修改内容
- [rules/10-ddd-modelling-process.md](rules/10-ddd-modelling-process.md) - 新增
  - 新增：完整的DDD建模流程规范，包含4个阶段12个步骤
  - 新增：战略设计（子域、限界上下文、上下文映射）
  - 新增：战术设计（领域概念、聚合边界、领域模型）
  - 新增：聚合设计4条铁律（必须严格遵守）
  - 新增：实体vs值对象对比
  - 新增：上下文映射模式说明
  - 新增：验收标准
  - 新增：参考资料

- [skills/ddd-analyzer/SKILL.md](skills/ddd-analyzer/SKILL.md) - 完全重写
  - 更新：description，强调遵循10-ddd-modelling-process.md
  - 新增：前置依赖规范，引用10-ddd-modelling-process.md
  - 重构：完整建模流程，分为4个阶段12个步骤
  - 新增：阶段一（对齐与理解）：检查文档、需求对齐
  - 新增：阶段二（战略架构）：识别子域、限界上下文、上下文映射
  - 新增：阶段三（战术设计）：提取领域概念、识别聚合边界、设计领域模型、梳理关系
  - 新增：阶段四（实现与验证）：识别功能点、生成文档、需求验证
  - 新增：聚合设计4条铁律（必须严格遵守）
  - 新增：实体vs值对象对比表格
  - 新增：上下文映射模式说明
  - 更新：执行流程，同步12步完整流程
  - 更新：项目配置，强调遵循10-ddd-modelling-process.md

- [rules/00-README.md](rules/00-README.md) - 更新
  - 新增：10-ddd-modelling-process.md 到快速开始列表
  - 更新：目录结构，添加新规则文件

---

## 2026-05-10 (之前最新)

### 修改时间
2026-05-10

### 修改原因
用户反馈ddd-analyzer的分析步骤跨度太大，缺少从限界上下文到领域模型之间的中间步骤。完善建模流程，增加更细分的分析步骤，确保DDD建模的完整性和逻辑性。

### 修改内容
- [skills/ddd-analyzer/SKILL.md](skills/ddd-analyzer/SKILL.md) - 更新
  - 新增：第四步"识别子域"，划分核心域、支撑域、通用域
  - 新增：第五步"提取领域概念"，系统提取名词、动词、业务规则
  - 新增：第六步"识别聚合边界"，确定聚合边界和聚合根
  - 更新：将原第四步"设计领域模型"改为第七步
  - 更新：后续所有步骤编号（梳理关系→第八步，识别功能点→第九步，生成文档→第十步，需求验证→第十一步）
  - 更新：执行流程，同步新增的分析步骤

---

## 2026-05-10 (之前最新)

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

