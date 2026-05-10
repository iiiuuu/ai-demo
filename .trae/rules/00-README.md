# 📖 项目规则索引

欢迎来到 ai-demo 项目！本文档是所有规则的快速索引。

## 📋 快速开始

作为AI Agent或开发者，你必须首先阅读并遵守：

1. **01-architecture-constraints.md** - DDD架构约束（红线！）
2. **02-java-coding-style.md** - Java编码规范
3. **03-spring-boot-guide.md** - Spring Boot全攻略
4. **04-mybatis-plus-guide.md** - MyBatis-Plus全攻略
5. **05-mapstruct-guide.md** - MapStruct全攻略
6. **06-spock-testing-guide.md** - Spock测试全攻略
7. **07-git-commit-message.md** - Git提交规范
8. **08-ddd-developer.md** - DDD开发者约束
9. **09-tdd-constraints.md** - TDD开发约束
10. **10-ddd-document-format.md** - DDD设计文档格式规范
11. **11-ddd-modelling-process.md** - DDD建模流程核心规范

## 🔴 最重要的5条规则（红线）

| # | 规则 | 出处 |
|---|------|------|
| 1 | Domain层**绝对禁止**导入MapStruct、MyBatis-Plus、Spring Web的类 | architecture-constraints.md |
| 2 | **所有跨层对象转换必须通过MapStruct**，禁止BeanUtils.copyProperties | java-coding-style.md |
| 3 | MyBatis-Plus的类**只能在infrastructure层使用** | mybatis-plus-guide.md |
| 4 | **强制使用TDD** - 先写失败测试，再实现代码 | tdd-constraints.md |
| 5 | 多功能点开发时必须按优先级执行，完成后检查剩余任务 | ddd-developer.md |

## 📁 目录结构
```
.trae/rules/
├── 00-README.md                          # 本文件 - 规则索引
├── 01-architecture-constraints.md        # DDD架构约束
├── 02-java-coding-style.md               # Java编码规范
├── 03-spring-boot-guide.md               # Spring Boot全攻略
├── 04-mybatis-plus-guide.md              # MyBatis-Plus全攻略
├── 05-mapstruct-guide.md                 # MapStruct全攻略
├── 06-spock-testing-guide.md             # Spock测试全攻略
├── 07-git-commit-message.md              # Git提交规范
├── 08-ddd-developer.md                   # DDD开发者约束
├── 09-tdd-constraints.md                 # TDD开发约束
├── 10-ddd-document-format.md             # DDD设计文档格式规范
├── 11-ddd-modelling-process.md           # DDD建模流程核心规范
└── templates/                            # 测试模板
    ├── testing-guide-template-application-service.md
    ├── testing-guide-template-domain-entity.md
    ├── testing-guide-template-repository-it.md
    ├── testing-guide-template-controller-it.md
    └── testing-guide-template-mapstruct.md
```




## 🎯 如何使用本文档

1. **AI Agent**: 在生成任何代码前，先查阅对应的规则
2. **开发者**: 在写代码前，先阅读规则确保符合规范
3. **Code Review**: 使用本文档作为审查标准

## 🚨 遇到问题？

如果发现规则有问题或需要更新，请创建issue或PR更新本文档。
