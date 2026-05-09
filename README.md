
# AI Demo

基于 DDD 架构的 Spring Boot 项目。

## 技术栈

- Spring Boot 3.2.0
- MyBatis-Plus 3.5.5
- MapStruct 1.5.5.Final
- Spock 2.3 + Groovy 4.0

## 项目结构

```
.trae/
├── rules/          # 项目规则
├── skills/         # Trae Skills
├── prompts/        # 提示词
└── config.yml      # 配置文件
```

## Skills

- `ddd-analyzer` - DDD分析建模
- `ddd-developer` - 根据DDD文档开发代码
- `changelog-recorder` - 记录配置变更

## 开发流程

遵循 6阶段TDD 开发流程。
