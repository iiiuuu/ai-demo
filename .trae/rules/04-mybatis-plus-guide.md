
# MyBatis-Plus 全攻略

## 🎯 版本要求
- **精确版本**: 3.5.5
- **只在 Infrastructure 层使用**

## 📁 文件位置要求

```
infrastructure/
└── persistence/
    ├── mapper/
    │   └── OrderMapper.java (使用 @Mapper 注解)
    ├── PO/
    │   └── OrderPO.java (持久化对象)
    ├── repository/
    │   └── OrderRepositoryImpl.java (Repository实现)
    └── assembler/
        └── OrderPersistenceMapper.java (MapStruct: DO ↔ PO)

src/main/resources/
└── mapper/
    └── OrderMapper.xml (复杂SQL)
```

## 🎯 使用规范

### Mapper接口
- ✅ 必须放在 `infrastructure.persistence.mapper` 包下
- ✅ 必须使用 `@Mapper` 注解
- ✅ 必须继承 `BaseMapper<PO>`
- ❌ 禁止在其他层直接注入和使用

### PO对象
- ✅ 必须放在 `infrastructure.persistence.PO` 包下
- ✅ 可以使用Lombok `@Data` 注解
- ✅ 必须使用MyBatis-Plus注解（如 `@TableName`）

### 复杂查询
- ✅ 必须在XML文件中定义SQL
- ❌ 严禁在Java代码中通过 `QueryWrapper` 嵌套大量条件

### 禁止使用
- ❌ `IService` / `ServiceImpl` (破坏DDD分层)
- ❌ 在RepositoryImpl之外使用Mapper
