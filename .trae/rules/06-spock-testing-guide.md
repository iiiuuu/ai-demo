
# Spock 测试全攻略

## 🎯 版本要求
- **Spock版本**: 2.3-groovy-4.0
- **Groovy版本**: 4.0.x (由Spock管理)
- **ArchUnit版本**: 1.2.1

## 📁 文件位置要求

```
src/test/groovy/
└── com/example/demoai/
    ├── ArchUnitConstraintsSpec.groovy (架构约束测试)
    ├── application/
    │   └── service/
    │       └── OrderApplicationServiceSpec.groovy
    ├── domain/
    │   ├── model/
    │   │   └── aggregate/
    │   │       └── OrderSpec.groovy
    │   └── repository/
    │       └── OrderRepositorySpec.groovy
    └── infrastructure/
        └── persistence/
            └── repository/
                └── OrderRepositoryImplSpec.groovy
```

## 🎯 测试规范

### 命名规范
- ✅ 测试类: `{被测类}Spec.groovy`
- ✅ 测试方法: `def "should {行为描述}"()`
- ✅ 集成测试: `{被测类}IT.groovy`

### Given-When-Then 结构
```groovy
def "should create order successfully"() {
    given: "an order request"
    def request = CreateOrderCommand.builder()
        .userId(1L)
        .build()

    when: "create order"
    def result = orderApplicationService.create(request)

    then: "order is created"
    result != null
    result.userId == 1L
}
```

### 强制要求
- ✅ 必须包含负面测试
- ✅ 必须测试边界条件
- ✅ 必须先写测试再实现

### 测试模板
- [标准单元测试模板](templates/testing-guide-template-application-service.md)
- [标准领域测试模板](templates/testing-guide-template-domain-entity.md)
- [集成测试模板](templates/testing-guide-template-repository-it.md)
- [Controller集成测试模板](templates/testing-guide-template-controller-it.md)
- [MapStruct测试模板](templates/testing-guide-template-mapstruct.md)
