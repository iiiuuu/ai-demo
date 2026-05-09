---
alwaysApply: true
scene: git_message
---
# Git 提交信息规范

## 格式
```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

## Type (类型)
| 类型 | 说明 |
|------|------|
| feat | 新增功能 |
| fix | 修复bug |
| docs | 文档变更 |
| refactor | 代码重构（不改变功能） |
| style | 格式化变更（不影响功能） |
| test | 测试相关变更 |
| ci | 持续集成相关变更 |
| chore | 构建工具或辅助工具变更 |

## Scope (范围) - 可选
- 代码变更的范围，例如：`application`, `domain`, `infrastructure`, `interface`

## Description (描述) 规范
- ✅ 不超过50个字符
- ✅ 以动词开头，使用现在时态（如"新增"而非"新增了"）
- ✅ 第一个字母小写
- ❌ 结尾不加句号
- ❌ 不能包含特殊字符（如：!、?、:等）
- ❌ 不能有多余空格

## 示例
```bash
# 简单示例
feat: 新增订单创建功能
fix: 修复库存扣减逻辑

# 带scope示例
feat(domain): 新增订单状态机
fix(infrastructure): 修复分页查询bug

# 完整示例
feat(application): 新增订单创建功能

- 添加CreateOrderCommand
- 添加OrderApplicationService
- 实现完整的订单创建流程

Closes: #123
```

## 禁止事项
- ❌ 一个commit message只包含一种类型变更
- ❌ 不要用中文全角冒号
- ❌ 不要有多余的空行
