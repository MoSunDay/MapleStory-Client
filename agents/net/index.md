Commit: a359863eb8b98abe97bcfc1f3dadc397f5e03f2d

# Net — 网络通信模块

## 职责

管理与 MapleStory v83 服务器的 TCP 连接、数据包序列化/反序列化、加密、协议路由。

## 边界与非目标

- **不负责** UI 或业务逻辑；仅负责网络层的发送/接收/路由
- **不负责** 游戏数据解析的业务含义解释（那是 Handler 调用方的职责）

## 关键抽象

### Session（`Session.h/.cpp`）
单例，管理 TCP 连接生命周期：`init()` 连接服务器(`ServerIP:ServerPort`)，`write()` 发送，`read()` 接收并处理，`reconnect()` 重连。内部持有 `Cryptography` 和 `PacketSwitch`。

### PacketSwitch（`PacketSwitch.h`）
基于 opcode 的处理器注册与分发。`forward()` 按 opcode 将接收到的数据包路由到注册的 `PacketHandler` 子类。最多支持 500 个 opcode。

### PacketHandler（`PacketHandler.h`）
数据包处理器的抽象基类。`PacketSwitch` 通过 opcode 索引持有 `unique_ptr<PacketHandler>` 数组。

### InPacket / OutPacket（`InPacket.h/.cpp`, `OutPacket.h/.cpp`）
二进制数据包序列化/反序列化。`InPacket` 支持按类型读取(short/int/string 等)，`OutPacket` 支持按类型写入并带有 opcode。

### Cryptography（`Cryptography.h/.cpp`）
数据包加密/解密，用于服务器通信。

### 传输层适配
通过编译宏 `USE_ASIO`（定义于 `MapleStory.h`）选择 `SocketAsio` 或 `SocketWinsock`。Linux 分支始终使用 Asio。

## 子模块

### Handlers（`Net/Handlers/`）
按业务域分组的入站数据包处理器（**13 个 handler 对**）：

- `LoginHandlers` — 登录流程握手
- `PlayerHandlers` — 玩家状态同步
- `InventoryHandlers` — 背包变更
- `MapObjectHandlers` — 地图对象（Mob、NPC、掉落物等）
- `AttackHandlers` — 攻击与技能
- `NpcInteractionHandlers` — NPC 对话状态
- `MessagingHandlers` — 聊天与消息
- `SetfieldHandlers` — 地图切换
- `CommonHandlers` — 通用消息
- **`CashShopHandlers`** — 现金商城操作处理
- **`PlayerInteractionHandlers`** — 玩家间交互（交易、组队等）
- **`TestingHandlers`** — 开发测试用

`Handlers/Helpers/` 包含子解析器：`LoginParser`、`CharacterParser`、`ItemParser`、`MovementParser`、`CashShopParser`（**比 mac 分支多 CashShopParser**）。

### Packets（`Net/Packets/`）
与 Handlers 对应的出站数据包构建器（**12 个文件**），均为头文件：

- `LoginPackets.h`、`SelectCharPackets.h`、`CharCreationPackets.h`
- `PlayerPackets.h`、`InventoryPackets.h`
- `AttackAndSkillPackets.h`、`MovementPacket.h`
- `GameplayPackets.h`、`CommonPackets.h`
- `MessagingPackets.h`、`NpcInteractionPackets.h`
- **`PlayerInteractionPackets.h`** — 玩家交互协议（linux 分支特有）

**无 `CustomPackets.h`**（mac 分支有自定义协议扩展）。

## 主流程

```
Session::read()
  → Socket::receive() 读取原始字节
  → Cryptography::decrypt() 解密
  → PacketSwitch::forward() 按 opcode 路由
  → PacketHandler::handle(InPacket) 解析并分发业务逻辑
```

## 依赖

- `Configuration` — 读取 ServerIP、ServerPort
- `Error` — 连接失败返回错误码
- `Template/` — Singleton、Optional、EnumMap 等
- `libs/NoLifeNx/` — 无直接依赖（网络层与游戏数据解耦）
