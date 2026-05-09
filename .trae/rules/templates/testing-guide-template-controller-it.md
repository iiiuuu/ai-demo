# 集成测试模板 (Controller 层)

> **适用场景**: 测试 REST Controller 层的 HTTP 端点
> **命名规范**: `被测试类名` + `IT`，如 `OrderControllerIT`
> **包路径**: `src/test/groovy/../integration`
> **基类要求**: 所有集成测试必须继承 `BaseAppSpec`

## BaseAppSpec 基类

```groovy
// 文件名: BaseAppSpec.groovy
import com.example.demoai.DemoAiApplication
import org.springframework.boot.test.context.SpringBootTest
import spock.lang.Specification
import org.springframework.boot.test.context.SpringBootTest.WebEnvironment

@SpringBootTest(classes = DemoAiApplication.class, webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class BaseAppSpec extends Specification {
    // 集成测试公共基类，提供 Spring Boot 测试环境
}
```

## Controller 集成测试示例

```groovy
// 文件名: OrderControllerIT.groovy
import spock.lang.Unroll
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.web.client.TestRestTemplate
import org.springframework.http.HttpEntity
import org.springframework.http.HttpMethod
import org.springframework.http.HttpStatus
import org.springframework.http.ResponseEntity

class OrderControllerIT extends BaseAppSpec {

    @Autowired
    TestRestTemplate restTemplate

    @Unroll
    def "should create order when POST /api/orders with valid request body"() {
        given: "构建请求体"
        def requestBody = [
            productId: "P001",
            quantity: 2
        ]

        when: "发送 POST 请求"
        ResponseEntity<Map> response = restTemplate.postForEntity(
            "/api/orders",
            new HttpEntity<>(requestBody),
            Map
        )

        then: "验证响应状态码和响应体"
        response.statusCode == HttpStatus.OK
        response.body.id != null
        response.body.status == "CREATED"
    }
    def "should return 400 when POST /api/orders with invalid request body"() {
        given: "构建无效请求体"
        def requestBody = [
            productId: "", // 无效 productId
            quantity: -1  // 无效 quantity
        ]

        when: "发送 POST 请求"
        ResponseEntity<Map> response = restTemplate.postForEntity(
            "/api/orders",
            new HttpEntity<>(requestBody),
            Map
        )

        then: "验证响应状态码为 400"
        response.statusCode == HttpStatus.BAD_REQUEST
    }
    
    def "should return order info when GET /api/orders/{id} with existing order id"() {
        given: "创建一个订单"
        def createRequest = [
            productId: "P001",
            quantity: 2
        ]
        ResponseEntity<Map> createResponse = restTemplate.postForEntity(
            "/api/orders",
            new HttpEntity<>(createRequest),
            Map
        )
        def orderId = createResponse.body.id

        when: "发送 GET 请求"
        ResponseEntity<Map> response = restTemplate.getForEntity(
            "/api/orders/${orderId}",
            Map
        )

        then: "验证响应状态码和订单详情"
        response.statusCode == HttpStatus.OK
        response.body.id == orderId
        response.body.productId == "P001"
    }
}
```