# User Context - 用户上下文领域模型设计

**文档状态**：NEW (外部上下文，当前阶段仅定义交互协议)

## 1. 上下文概述

- **限界上下文名称**：用户上下文 (User Context)
- **类型**：通用域 (Generic Domain)
- **主要职责**：用户信息管理、收货地址管理
- **边界范围**：用户基本信息、地址簿管理
- **当前阶段**：作为外部上下文，Order Context 通过防腐层读取用户和地址信息，暂不实现内部细节

## 2. 与 Order Context 的交互协议

### 防腐层接口（Order Context 侧）

```java
public interface UserService {
    UserInfo getUser(Long userId);
}

public class UserInfo {
    private Long userId;
    private String nickname;
    private String phone;
}

public interface AddressService {
    AddressInfo getAddress(Long addressId);
}

public class AddressInfo {
    private Long addressId;
    private String receiverName;
    private String phone;
    private String province;
    private String city;
    private String district;
    private String detail;
}
```

---

## 变更日志

| 版本 | 日期 | 变更描述 |
|-----|-----|---------|
| v1.0 | 2026-05-11 | 初始版本：定义用户上下文交互协议 |