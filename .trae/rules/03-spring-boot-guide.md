
# Spring Boot 全攻略

## 🎯 版本要求
- **精确版本**: 3.2.0
- **必须继承**: spring-boot-starter-parent

## 📁 项目结构要求
```
src/
├── main/
│   ├── java/
│   │   └── com/example/demoai/
│   │       └── AiDemoApplication.java (主类)
│   └── resources/
│       └── application.yml
└── test/
    └── groovy/
```

## ⚙️ 配置规范

### 配置文件
- ✅ 使用 `.yml` 而不是 `.properties`
- ✅ 主配置: `src/main/resources/application.yml`
- ✅ 环境配置: `application-{profile}.yml`

### 配置属性
- ✅ 使用 `kebab-case` 格式（如 `mybatis-plus.mapper-locations`）
- ✅ 敏感配置使用环境变量

## 📦 依赖要求

### Starter依赖
- ✅ 使用 `spring-boot-starter-web`
- ✅ 使用 `spring-boot-starter-validation`
- ✅ 使用 `spring-boot-starter-test` (测试用)

### 禁止的Starter
- ❌ `spring-boot-starter-data-jpa` (项目约定使用MyBatis-Plus)

## 🎯 注解使用规范

| 注解 | 使用位置 | 说明 |
|------|----------|------|
| `@SpringBootApplication` | 主类 | 应用入口 |
| `@RestController` | Controller类 | Interface层 |
| `@RequestMapping` | Controller类/方法 | 路由映射 |
| `@Service` | ApplicationService类 | Application层 |
| `@Autowired` | 依赖注入字段 | 仅在合法层级使用 |
| `@Transactional` | ApplicationService方法 | 事务边界 |

## 🚨 禁止事项

- ❌ 禁止在Domain层使用任何Spring注解
- ❌ 禁止在Application层使用MyBatis-Plus注解
- ❌ 禁止在Infrastructure层之外使用MyBatis-Plus Mapper
