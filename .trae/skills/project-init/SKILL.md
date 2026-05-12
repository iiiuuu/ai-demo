---
name: "project-init"
description: "初始化一个基于DDD四层架构的Spring Boot项目，包含固定技术栈和完整目录结构。当用户要求创建新项目、初始化项目、搭建项目骨架时调用。"
---

# 项目初始化

---

## 1. 功能概述

本 Skill 用于**从零初始化**一个基于 **DDD 四层架构** 的 Spring Boot 项目。核心功能包括：

- **项目骨架搭建**：自动创建符合 DDD 四层架构的完整目录结构和包结构
- **依赖管理**：按固定版本引入 Spring Boot、MyBatis-Plus、MapStruct、Spock、ArchUnit 等核心依赖
- **基础配置生成**：生成 `application.yml` 配置文件，包含数据源、MyBatis-Plus 等基础配置
- **架构约束测试**：自动生成 ArchUnit 架构约束测试，确保分层依赖不被违反
- **构建验证**：编译项目并执行全部测试用例，确保初始化后的项目可正常构建和运行
- **服务健康检查**：启动服务并验证运行状态，确保项目可正常启动

初始化完成后，项目即具备完整的 DDD 四层架构骨架，可直接进入业务功能开发。

---

## 2. 使用时机

在以下场景中调用本 Skill：

| 场景 | 说明 |
|------|------|
| **新建项目** | 用户要求"创建新项目"、"初始化项目"、"搭建项目骨架" |
| **项目重置** | 需要从头重建项目结构时 |
| **技术栈升级后重建** | 技术栈版本更新后，需要按新规范重建项目 |
| **DDD 架构迁移** | 将旧项目迁移到 DDD 四层架构时 |

**不适用场景**：
- 已有项目添加新功能（应使用 `ddd-developer` Skill）
- 已有项目进行 DDD 领域建模（应使用 `ddd-analyzer` Skill）

---

## 3. 前置依赖

### 3.1 环境要求

| 环境 | 版本要求 | 验证命令 |
|------|---------|---------|
| **JDK** | 17+ | `java -version` |
| **Maven** | 3.8+ | `mvn -version` |

### 3.2 固定技术栈（必须严格遵守）

| 技术 | 精确版本 | 用途 | 约束 |
|------|---------|------|------|
| **Spring Boot** | 3.2.0 | Web 框架 | 必须继承 `spring-boot-starter-parent` |
| **MyBatis-Plus** | 3.5.5 | ORM | **只能在 Infrastructure 层使用** |
| **MapStruct** | 1.5.5.Final | 对象转换 | 必须使用 `@Mapper(componentModel = "spring")` |
| **Spock** | 2.3-groovy-4.0 | 测试框架 | 测试文件使用 Groovy 编写 |
| **ArchUnit** | 1.2.1 | 架构约束测试 | 验证 DDD 分层依赖 |
| **Java** | 17+ | 编程语言 | Spring Boot 3.2.0 最低要求 |
| **Maven** | - | 构建工具 | 使用 `pom.xml` |
| **H2** | - | 内存数据库 | 开发/测试阶段使用，通过 MyBatis-Plus 连接 |

### 3.3 前置检查清单

执行初始化前，**必须确认**以下条件已满足：

- [ ] JDK 17+ 已安装且 `JAVA_HOME` 已配置
- [ ] Maven 3.8+ 已安装且 `mvn` 命令可用
- [ ] 端口 `8080` 未被占用（Spring Boot 默认端口）

---

## 4. 执行步骤

### 步骤 1：创建 Maven 项目结构（pom.xml）

在项目根目录创建 `pom.xml`，**必须包含上述所有依赖的精确版本**。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>demoai</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demoai</name>

    <properties>
        <java.version>17</java.version>
        <mybatis-plus.version>3.5.5</mybatis-plus.version>
        <mapstruct.version>1.5.5.Final</mapstruct.version>
        <spock.version>2.3-groovy-4.0</spock.version>
        <archunit.version>1.2.1</archunit.version>
    </properties>

    <dependencies>
        <!-- Spring Boot -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>

        <!-- MyBatis-Plus -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-spring-boot3-starter</artifactId>
            <version>${mybatis-plus.version}</version>
        </dependency>

        <!-- H2 内存数据库 -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>

        <!-- MapStruct -->
        <dependency>
            <groupId>org.mapstruct</groupId>
            <artifactId>mapstruct</artifactId>
            <version>${mapstruct.version}</version>
        </dependency>
        <dependency>
            <groupId>org.mapstruct</groupId>
            <artifactId>mapstruct-processor</artifactId>
            <version>${mapstruct.version}</version>
            <scope>provided</scope>
        </dependency>

        <!-- Test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.spockframework</groupId>
            <artifactId>spock-core</artifactId>
            <version>${spock.version}</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.spockframework</groupId>
            <artifactId>spock-spring</artifactId>
            <version>${spock.version}</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.tngtech.archunit</groupId>
            <artifactId>archunit-junit5</artifactId>
            <version>${archunit.version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.codehaus.gmavenplus</groupId>
                <artifactId>gmavenplus-plugin</artifactId>
                <version>3.0.2</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>compileTests</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

---

### 步骤 2：创建 DDD 四层目录结构

在 `src/main/java/com/example/demoai/` 下创建以下包结构：

```
com.example.demoai
├── AiDemoApplication.java              # Spring Boot 主类
├── interface                            # 用户接口层
│   ├── controller/                      # REST Controller
│   ├── dto/                             # RequestDTO / ResponseDTO
│   └── assembler/                       # MapStruct: RequestDTO ↔ Command
├── application                          # 应用层
│   ├── service/                         # ApplicationService
│   ├── command/                         # Command 对象
│   ├── BO/                              # Business Object（返回给 Interface 层）
│   └── assembler/                       # MapStruct: DO ↔ BO
├── domain                               # 领域层（核心）
│   ├── model/
│   │   ├── aggregate/                   # 聚合根
│   │   └── valueobject/                 # 值对象
│   ├── service/                         # DomainService
│   └── repository/                      # Repository 接口
└── infrastructure                       # 基础设施层
    ├── persistence/
    │   ├── mapper/                      # MyBatis-Plus Mapper
    │   ├── PO/                          # 持久化对象
    │   ├── assembler/                   # MapStruct: DO ↔ PO
    │   └── repository/                  # Repository 实现
    └── config/                          # 基础设施配置
```

测试目录 `src/test/groovy/com/example/demoai/`：

```
com.example.demoai
├── ArchUnitConstraintsSpec.groovy       # 架构约束测试
├── application/
│   └── service/                         # ApplicationService 测试
├── domain/
│   ├── model/
│   │   └── aggregate/                   # 聚合根测试
│   └── repository/                      # Repository 接口测试
└── infrastructure/
    └── persistence/
        └── repository/                  # Repository 实现集成测试
```

---

### 步骤 3：创建 Spring Boot 主类

文件路径：`src/main/java/com/example/demoai/AiDemoApplication.java`

```java
package com.example.demoai;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class AiDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(AiDemoApplication.class, args);
    }
}
```

---

### 步骤 4：创建 application.yml 配置

文件路径：`src/main/resources/application.yml`

```yaml
spring:
  datasource:
    url: jdbc:h2:mem:demoai;DB_CLOSE_DELAY=-1;MODE=MySQL
    username: sa
    password:
    driver-class-name: org.h2.Driver
  h2:
    console:
      enabled: true
      path: /h2-console

mybatis-plus:
  mapper-locations: classpath:mapper/**/*.xml
  configuration:
    map-underscore-to-camel-case: true
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    db-config:
      id-type: auto
      logic-delete-field: deleted
      logic-delete-value: 1
      logic-not-delete-value: 0
```

---

### 步骤 5：创建 ArchUnit 架构约束测试

文件路径：`src/test/groovy/com/example/demoai/ArchUnitConstraintsSpec.groovy`

```groovy
package com.example.demoai

import com.tngtech.archunit.core.importer.ClassFileImporter
import com.tngtech.archunit.lang.syntax.ArchRuleDefinition
import spock.lang.Specification

class ArchUnitConstraintsSpec extends Specification {

    def "Domain层不能依赖Spring、MyBatis-Plus、MapStruct"() {
        given:
        def importedClasses = new ClassFileImporter().importPackages("com.example.demoai")

        expect:
        ArchRuleDefinition.noClasses()
            .that().resideInAPackage("..domain..")
            .should().dependOnClassesThat()
            .resideInAnyPackage(
                "org.springframework..",
                "com.baomidou..",
                "org.mapstruct.."
            )
            .check(importedClasses)
    }

    def "Infrastructure层不能依赖Interface层和Application层"() {
        given:
        def importedClasses = new ClassFileImporter().importPackages("com.example.demoai")

        expect:
        ArchRuleDefinition.noClasses()
            .that().resideInAPackage("..infrastructure..")
            .should().dependOnClassesThat()
            .resideInAnyPackage(
                "..interface..",
                "..application.."
            )
            .check(importedClasses)
    }
}
```

---

### 步骤 6：服务运行状态检查与测试用例执行

完成上述 5 个步骤后，**必须执行**以下验证流程，确保项目初始化正确。

#### 6.1 验证流程

```
┌─────────────────────────────────────┐
│  步骤 6：编译项目                     │
│  mvn clean compile -DskipTests       │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  步骤 6：执行全部测试用例             │
│  mvn clean test                      │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  步骤 6：启动服务并验证运行状态        │
│  mvn spring-boot:run（后台启动）      │
│  等待服务启动 → curl 健康检查          │
└──────────────┬──────────────────────┘
               │
               ▼
        ┌──────────────┐
        │  是否全部通过？ │
        └──┬────────┬──┘
           │ 是     │ 否
           ▼        ▼
    ┌──────────┐  ┌──────────────────────┐
    │ 初始化成功 │  │ 分析错误 → 修复 → 重试  │
    └──────────┘  │ （最多 3 次）           │
                  └──────────┬───────────┘
                             │
                             ▼
                      ┌──────────────┐
                      │ 3次后仍失败？  │
                      └──┬────────┬──┘
                         │ 是     │ 否
                         ▼        │
                  ┌──────────────┐│
                  │ 输出错误报告   ││
                  │ 终止流程      ││
                  └──────────────┘│
                                  │
                                  ▼
                          返回重试循环
```

#### 6.2 详细操作

**6.2.1 编译项目**

```bash
mvn clean compile -DskipTests
```

- 检查编译输出，确认 `BUILD SUCCESS`
- 若编译失败，分析错误信息并修复，然后重新编译

**6.2.2 执行测试用例**

```bash
mvn clean test
```

- 检查测试报告，确认所有测试用例通过
- 测试报告位置：`target/surefire-reports/`
- 若测试失败，分析失败原因并修复，然后重新执行测试

**6.2.3 启动服务并验证运行状态**

```bash
# 后台启动服务
mvn spring-boot:run &

# 等待服务启动（最多等待 60 秒）
# 轮询检查服务状态
for i in $(seq 1 30); do
  if curl -s http://localhost:8080/actuator/health > /dev/null 2>&1; then
    echo "服务启动成功"
    break
  fi
  sleep 2
done
```

- 若服务启动失败（超时或异常退出），分析日志并修复，然后重新启动
- 若项目未引入 Actuator，则使用 `curl http://localhost:8080/` 检查是否返回非错误响应

#### 6.3 重试机制

| 项目 | 说明 |
|------|------|
| **最大重试次数** | 3 次 |
| **重试触发条件** | 编译失败、测试未通过、服务启动异常中任意一项 |
| **每次重试前** | 必须分析错误原因并修复，不得直接重试 |
| **3 次后仍失败** | 输出详细错误报告，终止初始化流程 |

#### 6.4 错误报告格式

当 3 次重试后仍无法通过时，输出以下格式的错误报告：

```markdown
## ❌ 项目初始化失败报告

**失败时间**：{YYYY-MM-DD HH:mm:ss}
**重试次数**：3/3

### 失败阶段
{编译失败 / 测试失败 / 服务启动失败}

### 错误详情
{完整的错误堆栈或日志}

### 已尝试的修复措施
1. {第 1 次修复措施}
2. {第 2 次修复措施}
3. {第 3 次修复措施}

### 建议
{基于错误分析给出的解决建议}
```

---

## 5. 输出规范

### 5.1 输出内容

初始化成功后，项目根目录下应包含以下文件和目录：

```
{项目根目录}/
├── pom.xml                              # Maven 项目配置
├── src/
│   ├── main/
│   │   ├── java/com/example/demoai/
│   │   │   ├── AiDemoApplication.java   # Spring Boot 主类
│   │   │   ├── interface/               # 用户接口层（空包结构）
│   │   │   ├── application/             # 应用层（空包结构）
│   │   │   ├── domain/                  # 领域层（空包结构）
│   │   │   └── infrastructure/          # 基础设施层（空包结构）
│   │   └── resources/
│   │       └── application.yml          # 应用配置
│   └── test/
│       └── groovy/com/example/demoai/
│           └── ArchUnitConstraintsSpec.groovy  # 架构约束测试
└── target/                              # 构建输出（编译后生成）
```

### 5.2 输出格式

初始化完成后，输出以下格式的成功报告：

```markdown
## ✅ 项目初始化成功

**项目名称**：demoai
**基础包路径**：com.example.demoai
**架构模式**：DDD 四层架构

### 技术栈
| 技术 | 版本 |
|------|------|
| Spring Boot | 3.2.0 |
| MyBatis-Plus | 3.5.5 |
| MapStruct | 1.5.5.Final |
| Spock | 2.3-groovy-4.0 |
| ArchUnit | 1.2.1 |
| Java | 17+ |

### 验证结果
- ✅ 编译通过
- ✅ 测试通过（{N} 个测试用例全部通过）
- ✅ 服务启动正常（端口 8080）

### 下一步
项目已就绪，可使用 `ddd-analyzer` 进行 DDD 领域建模。
```

---

## 6. 项目配置

### 6.1 配置文件格式

- **格式**：YAML（`.yml`），**禁止使用 `.properties`**
- **位置**：`src/main/resources/application.yml`
- **编码**：UTF-8

### 6.2 配置参数说明

#### 数据源配置（spring.datasource）

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `url` | `jdbc:h2:mem:demoai;DB_CLOSE_DELAY=-1;MODE=MySQL` | H2 内存数据库连接，`MODE=MySQL` 兼容 MySQL 语法 |
| `username` | `sa` | 数据库用户名 |
| `password` | （空） | 数据库密码 |
| `driver-class-name` | `org.h2.Driver` | H2 JDBC 驱动 |

#### H2 控制台配置（spring.h2.console）

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `enabled` | `true` | 启用 H2 Web 控制台 |
| `path` | `/h2-console` | 控制台访问路径 |

#### MyBatis-Plus 配置（mybatis-plus）

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `mapper-locations` | `classpath:mapper/**/*.xml` | Mapper XML 文件位置 |
| `configuration.map-underscore-to-camel-case` | `true` | 下划线转驼峰命名 |
| `configuration.log-impl` | `StdOutImpl` | SQL 日志输出（生产环境应关闭） |
| `global-config.db-config.id-type` | `auto` | 主键生成策略 |
| `global-config.db-config.logic-delete-field` | `deleted` | 逻辑删除字段名 |
| `global-config.db-config.logic-delete-value` | `1` | 已删除标记值 |
| `global-config.db-config.logic-not-delete-value` | `0` | 未删除标记值 |

### 6.3 环境变量

H2 内存数据库无需额外环境变量配置。切换到 MySQL 等外部数据库时，需配置对应的连接信息。

### 6.4 多环境配置

如需区分环境，创建以下文件：

- `application-dev.yml` — 开发环境
- `application-test.yml` — 测试环境
- `application-prod.yml` — 生产环境

通过 `spring.profiles.active` 激活指定环境。

---

## 🚨 初始化约束（红线）

执行项目初始化时，**必须严格遵守以下约束**：

| # | 约束 | 参考规则 |
|---|------|---------|
| 1 | 所有依赖版本**必须使用上述固定版本**，不得随意升级或降级 | 各技术栈 Guide |
| 2 | 目录结构**必须严格遵循 DDD 四层架构**，不得自行增减层级 | [01-architecture-constraints.md](file:///Users/xulong/code/ai-demo/.trae/rules/01-architecture-constraints.md) |
| 3 | Domain 层**绝对禁止**导入 MapStruct、MyBatis-Plus、Spring Web 的类 | [01-architecture-constraints.md](file:///Users/xulong/code/ai-demo/.trae/rules/01-architecture-constraints.md) |
| 4 | 配置文件**必须使用 `.yml` 格式**，不得使用 `.properties` | [03-spring-boot-guide.md](file:///Users/xulong/code/ai-demo/.trae/rules/03-spring-boot-guide.md) |
| 5 | 测试文件**必须使用 Groovy + Spock**，放在 `src/test/groovy/` 下 | [06-spock-testing-guide.md](file:///Users/xulong/code/ai-demo/.trae/rules/06-spock-testing-guide.md) |
| 6 | **必须创建 ArchUnit 架构约束测试**，确保分层依赖不被违反 | [01-architecture-constraints.md](file:///Users/xulong/code/ai-demo/.trae/rules/01-architecture-constraints.md) |
| 7 | 包名**必须使用 `com.example.demoai`** 作为基础包 | [02-java-coding-style.md](file:///Users/xulong/code/ai-demo/.trae/rules/02-java-coding-style.md) |
| 8 | 初始化完成后**必须执行步骤 6 的验证流程**，未通过验证不得进入开发阶段 | 本 Skill |

---

## 📚 参考规则文档

初始化完成后，AI Agent 必须阅读并遵守以下规则：

| 规则文件 | 说明 |
|---------|------|
| [01-architecture-constraints.md](file:///Users/xulong/code/ai-demo/.trae/rules/01-architecture-constraints.md) | DDD 四层架构约束 |
| [02-java-coding-style.md](file:///Users/xulong/code/ai-demo/.trae/rules/02-java-coding-style.md) | Java 编码规范 |
| [03-spring-boot-guide.md](file:///Users/xulong/code/ai-demo/.trae/rules/03-spring-boot-guide.md) | Spring Boot 全攻略 |
| [04-mybatis-plus-guide.md](file:///Users/xulong/code/ai-demo/.trae/rules/04-mybatis-plus-guide.md) | MyBatis-Plus 全攻略 |
| [05-mapstruct-guide.md](file:///Users/xulong/code/ai-demo/.trae/rules/05-mapstruct-guide.md) | MapStruct 全攻略 |
| [06-spock-testing-guide.md](file:///Users/xulong/code/ai-demo/.trae/rules/06-spock-testing-guide.md) | Spock 测试全攻略 |
| [09-tdd-constraints.md](file:///Users/xulong/code/ai-demo/.trae/rules/09-tdd-constraints.md) | TDD 开发约束 |