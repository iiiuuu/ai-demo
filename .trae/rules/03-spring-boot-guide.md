
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

---

## 📋 pom.xml 完整配置模板

这是项目必须使用的 pom.xml 完整配置，包含所有必需的依赖、构建插件、编译配置和测试配置。

### 完整 pom.xml 模板

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
    <artifactId>ai-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>ai-demo</name>
    <description>DDD Demo Project</description>

    <properties>
        <java.version>17</java.version>
        <mybatis-plus.version>3.5.5</mybatis-plus.version>
        <mapstruct.version>1.5.5.Final</mapstruct.version>
        <lombok.version>1.18.30</lombok.version>
        <spock.version>2.3-groovy-4.0</spock.version>
        <groovy.version>4.0.16</groovy.version>
        <archunit.version>1.2.1</archunit.version>
    </properties>

    <dependencies>
        <!-- Spring Boot Starters -->
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

        <!-- H2 Database -->
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

        <!-- Lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>${lombok.version}</version>
            <scope>provided</scope>
        </dependency>

        <!-- Spring Boot Test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- Spock Testing -->
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
            <groupId>org.apache.groovy</groupId>
            <artifactId>groovy</artifactId>
            <version>${groovy.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- ArchUnit -->
        <dependency>
            <groupId>com.tngtech.archunit</groupId>
            <artifactId>archunit-junit5</artifactId>
            <version>${archunit.version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!-- Spring Boot Maven Plugin -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>

            <!-- Maven Compiler Plugin (Java & MapStruct) -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <annotationProcessorPaths>
                        <path>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                            <version>${lombok.version}</version>
                        </path>
                        <path>
                            <groupId>org.mapstruct</groupId>
                            <artifactId>mapstruct-processor</artifactId>
                            <version>${mapstruct.version}</version>
                        </path>
                        <path>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok-mapstruct-binding</artifactId>
                            <version>0.2.0</version>
                        </path>
                    </annotationProcessorPaths>
                </configuration>
            </plugin>

            <!-- GMavenPlus Plugin (Groovy Compilation) -->
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

            <!-- Maven Surefire Plugin (Spock Test Execution) -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.2.2</version>
                <configuration>
                    <includes>
                        <include>**/*Spec.java</include>
                        <include>**/*Spec.groovy</include>
                    </includes>
                    <useSystemClassLoader>false</useSystemClassLoader>
                </configuration>
            </plugin>

            <!-- Maven Failsafe Plugin (Integration Tests) -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-failsafe-plugin</artifactId>
                <version>3.2.2</version>
                <configuration>
                    <includes>
                        <include>**/*IT.java</include>
                        <include>**/*IT.groovy</include>
                    </includes>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>integration-test</goal>
                            <goal>verify</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>

        <!-- Groovy Test Source Directory -->
        <testSourceDirectory>src/test/groovy</testSourceDirectory>
    </build>
</project>
```

### 配置项详细说明

| 配置项 | 作用 | 必要性 |
|--------|------|--------|
| `spring-boot-starter-parent` 3.2.0 | Spring Boot 父 POM，提供依赖管理 | ✅ 必须 |
| `maven-compiler-plugin` | Java 编译配置，包含 MapStruct 注解处理器 | ✅ 必须 |
| `gmavenplus-plugin` | Groovy 编译，用于 Spock 测试 | ✅ 必须 |
| `maven-surefire-plugin` | 单元测试执行，配置为运行 `*Spec.*` | ✅ 必须 |
| `maven-failsafe-plugin` | 集成测试执行，配置为运行 `*IT.*` | ✅ 必须 |
| `annotationProcessorPaths` | Lombok + MapStruct 注解处理器配置 | ✅ 必须 |
| `testSourceDirectory` | 指定 Groovy 测试源码目录 | ✅ 必须 |

### 关键依赖版本说明

| 依赖 | 版本 | 说明 |
|------|------|------|
| Spring Boot | 3.2.0 | 项目核心框架 |
| MyBatis-Plus | 3.5.5 | ORM 框架 |
| MapStruct | 1.5.5.Final | 对象映射框架 |
| Lombok | 1.18.30 | 简化 Java 代码 |
| Spock | 2.3-groovy-4.0 | 测试框架 |
| Groovy | 4.0.16 | Spock 使用的语言 |
| ArchUnit | 1.2.1 | 架构约束测试 |

### 测试文件命名约定

- **单元测试**: `*Spec.groovy`（由 maven-surefire-plugin 执行）
- **集成测试**: `*IT.groovy`（由 maven-failsafe-plugin 执行）
