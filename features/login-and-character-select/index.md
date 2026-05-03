Commit: c8a7aaeaa283edd20422e7c46209c9e8a5e2504d

# 登录与角色选择

## 概述

从游戏启动到进入游戏世界的完整认证与角色选择流程。

## 触发者

玩家启动客户端后自动触发。

## 流程

### 1. Logo 动画（UILogo）
游戏启动后显示 Logo 画面，可按键跳过进入登录界面。

### 2. 世界/频道选择（UIWorldSelect → UIChannel）
- 从服务器获取世界列表
- 选择世界 → 选择频道
- 客户端发送世界/频道信息到服务器

### 3. 登录认证（UILogin）
- 输入账号、密码、PIN（可选）
- 支持保存账号（`SaveLogin` 配置项）
- 认证成功后进入角色选择或角色创建界面
- 可能触发 UILoginNotice（登录公告）、UITermsOfService（服务条款）

### 4. 角色选择（UICharSelect）
- 从服务器获取该账号下的角色列表（`CharEntry` 列表）
- 玩家可选择已有角色 → 进入游戏世界
- 或创建新角色 → 进入角色创建流程

### 5. 角色创建
- **UIGender** → 选择性别
- **UIRaceSelect** → 选择种族（冒险家、骑士团、战神 等）
- **UIExplorerCreation** / **UIAranCreation** / **UICygnusCreation** → 角色外观定制（发型、脸型、服装、武器）并命名
- 创建成功后回到角色选择界面

### 6. 等待进入
- **UILoginwait** — 加载等待界面，直到服务器发送地图切换（Setfield）数据包

## 关键状态

| 状态 | 说明 |
|---|---|
| 未连接 | 服务器未启动或网络故障，显示重试选项 |
| 登录中 | 等待服务器认证响应 |
| 角色列表已加载 | 可选择/创建角色 |
| 进入中 | 等待地图数据加载 |

## 错误面

- **CONNECTION**: 服务器连接失败，可重试
- **MISSING_FILE**: 缺少必要 NX 文件
- 认证失败：账号密码错误，服务器返回错误消息
- 角色名重复：创建角色时名字已被占用

## 相关模块

- [agents/net/index.md](../../agents/net/index.md) — 登录握手协议（LoginHandlers、LoginPackets）
- [agents/io/index.md](../../agents/io/index.md) — 所有登录/角色创建 UI
- [agents/character/index.md](../../agents/character/index.md) — CharEntry、角色数据
- [Configuration](../../../Configuration.h) — ServerIP、ServerPort、SaveLogin 等
