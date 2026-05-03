Commit: a359863eb8b98abe97bcfc1f3dadc397f5e03f2d

# 聊天与消息

## 概述

玩家在游戏中的文本通信系统，包括公开聊天、私聊、系统消息等。

## 触发者

- 玩家输入文本（聊天栏）
- 服务器推送（系统消息、其他玩家消息）

## 消息类型

| 类型 | 说明 |
|---|---|
| 公聊（All） | 当前地图所有玩家可见 |
| 私聊（Whisper） | 指定玩家一对一消息 |
| 好友聊天（Buddy） | 好友列表内消息 |
| 组队聊天（Party） | 队伍成员可见 |
| 公会聊天（Guild） | 同公会成员可见 |
| 系统通知 | 服务器广播、GM 消息等 |

## UI 组件

- **UIChatbar** — 底部聊天输入栏，支持快捷键切换频道
- **UIChat** — 聊天记录显示区域，支持滚动、频道筛选
- **ChatBalloon** — 角色头顶气泡（`Char.speak()`），由 `IO/Components/ChatBalloon` 组件实现
- **ScrollingNotice** — 滚动公告栏（`IO/Components/ScrollingNotice`）
- **UINotice** — 弹窗公告
- **UIStatusMessenger** — 状态消息提示（linux 分支具备）

## 输入流程

1. 按 Enter 激活聊天栏
2. 输入文本（可选格式标签如颜色）
3. 按 Enter 发送
4. 通过 `MessagingPackets` 构建出站数据包
5. 服务器广播给目标接收者
6. `MessagingHandlers` 处理入站消息并显示

## 约束

- 消息长度受服务器限制
- 部分格式标签由服务器过滤
- 气泡显示有时长限制

## 相关模块

- [agents/io/index.md](../../agents/io/index.md) — UIChat、UIChatbar、ChatBalloon、UIStatusMessenger
- [agents/net/index.md](../../agents/net/index.md) — MessagingHandlers、MessagingPackets
- [agents/character/index.md](../../agents/character/index.md) — Char.speak()
