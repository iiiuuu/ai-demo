# 标准领域实体测试模板

> **适用场景**: 测试 Domain Entity 或 Aggregate Root 类
> **命名规范**: `被测试类名` + `Spec`，如 `OrderSpec`

```groovy
// 文件名: OrderSpec.groovy
class OrderSpec extends Specification {

    @Unroll
    def "should transition to #expectedStatus when pay() is called from #currentStatus" () {
        given: "一个处于特定状态的订单"
        def order = new Order()
        order.status = currentStatus

        when: "执行支付操作"
        order.pay()

        then: "验证最终状态或异常"
        order.status == expectedStatus

        where:
        currentStatus     || expectedStatus
        OrderStatus.DRAFT || OrderStatus.PAID
    }
    
    def "should throw OrderAlreadyPaidException when pay() is called on an already paid order" () {
        given: "一个已支付订单"
        def order = new Order()
        order.status = OrderStatus.PAID

        when: "再次支付"
        order.pay()

        then: "抛出特定领域异常"
        thrown(OrderAlreadyPaidException)
    }
}
```