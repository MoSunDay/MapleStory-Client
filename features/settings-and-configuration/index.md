Commit: c8a7aaeaa283edd20422e7c46209c9e8a5e2504d

# 设置与配置

## 概述

游戏运行时配置的管理与持久化。配置在 `Settings` 文件中以 `name = value` 格式存储。

## 触发者

- 游戏启动时自动加载
- 选项菜单（`UIOptionMenu`）中的用户操作
- 游戏关闭时自动保存

## 配置项

### 连接
- `ServerIP` — 服务器 IP 地址（默认 `127.0.0.1`）
- `ServerPort` — 服务器端口（默认 `8484`）

### 显示
- `Fullscreen` — 全屏模式（默认 `false`）
- `Width` / `Height` — 窗口分辨率（默认 800×600）
- `VSync` — 垂直同步（默认 `true`）
- 字体路径（`FontPathNormal`、`FontPathBold`）

### 音频
- `BGMVolume` — 背景音量（0-100，默认 50）
- `SFXVolume` — 音效音量（0-100，默认 50）

### 账号
- `SaveLogin` — 是否保存账号名
- `DefaultAccount` — 上次使用的账号
- `DefaultWorld` / `DefaultChannel` / `DefaultCharacter` — 上次选择

### UI 位置
共 14 个可调整界面位置：`PosSTATS`、`PosEQINV`、`PosINV`、`PosSKILL`、`PosQUEST`、`PosMAP`、`PosUSERLIST`、`PosCHAT`、`PosMINIMAP`、`PosSHOP`、`PosNOTICE`、`PosMAPLECHAT`、`PosCHANNEL`、`PosJOYPAD`、`PosEVENT`、`PosKEYCONFIG`、`PosOPTIONMENU`

### 其他
- `Chatopen` — 聊天栏是否默认打开
- `show_weekly` — 是否显示每周之星
- `rightclicksell` — 右键出售功能开关

## 关键状态

| 状态 | 说明 |
|---|---|
| 已加载 | 配置文件存在且全部解析成功 |
| 使用默认值 | 配置文件缺失或某条目缺失，使用 Configuration.h 中的硬编码默认值 |
| 已保存 | 退出时写入 Settings 文件 |

## 约束

- 配置值类型固定（bool / string / int / point），不支持动态类型
- 分辨率受系统硬件限制（`HardwareInfo`）
- `SaveLogin` 关闭时不会保存账号名

## 相关模块

- [Configuration.h](../../Configuration.h) — 所有配置项定义、Entry 类型、Setting 模板
- [Configuration.cpp](../../Configuration.cpp) — load/save 实现
- [agents/io/index.md](../../agents/io/index.md) — UIOptionMenu（选项菜单 UI）
- [Util/HardwareInfo](../../Util/HardwareInfo.h) — 硬件上限检测
