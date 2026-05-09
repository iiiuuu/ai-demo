# 标准MapStruct测试模板

> **适用场景**: 测试 各层mapper的转换类
> **命名规范**: `被测试类名`+`层名` + `Spec`，如 `OrderApplicationMapper`

## 测试要求

- 每个Mapper接口必须有对应的单元测试类，使用Spock框架编写，覆盖所有映射方法，特别是不同名字段、类型转换、忽略字段的情况
- 测试类命名规范:
  - {Mapper接口名}Spec，例如 OrderApplicationMapperSpec
- 位置: 与接口同在的测试源集下镜像包结构，例如:
  src/test/java/com/example/demoai/application/assembler/OrderApplicationMapperSpec.java
- 测试模板:

```goovy
class OrderApplicationMapperSpec extends Specification {
    @Autowired
    private OrderApplicationMapper orderApplicationMapper;

    def "should map CreateOrderCommand to Order correctly"() {
        given:
        CreateOrderCommand command = new CreateOrderCommand(...)

        when:
        Order order = orderApplicationMapper.toDO(command)

        then:
        // 断言映射结果正确，特别是不同名字段和类型转换
    }

    def "should map Order to OrderBO correctly"() {
        given:
        Order order = new Order(...)

        when:
        OrderBO orderBO = orderApplicationMapper.toBO(order)

        then:
        // 断言映射结果正确，特别是不同名字段和类型转换
    }
}
```

