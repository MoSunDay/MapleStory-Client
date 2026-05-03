Commit: cbb0fe27cf9683a12eca0a569121d8033cdc2a4d

# 设置与配置

## 概述

游戏运行时配置的管理与持久化。配置在 `Settings` 文件中以 `name = value` 格式存储。`Configuration` 类继承 `Singleton<Configuration>`，在构造时加载，析构时保存。

## 触发者

- 游戏启动时自动加载（`Configuration::load()`）
- 选项菜单（`UIOptionMenu`）中的用户操作
- 游戏关闭时自动保存（`~Configuration()`）

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
- 登录音乐（`LoginMusic`、`LoginMusicSEA`、`LoginMusicNewtro`）

### 账号
- `SaveLogin` — 是否保存账号名
- `DefaultAccount` — 上次使用的账号
- `DefaultWorld` / `DefaultChannel` / `DefaultCharacter` — 上次选择
- `DefaultRegion` — 上次区域选择（默认 5）
- 自动登录配置：`auto_world`、`auto_channel`、`auto_acc`、`auto_pass`、`auto_pic`、`auto_cid`
  - 访问器：`get_auto_login()`、`get_auto_world()`、`get_auto_channel()` 等

### UI 位置
共 18 个可调整界面位置，均为 `PointEntry` 类型：
`PosSTATS`、`PosEQINV`、`PosINV`、`PosSKILL`、`PosQUEST`、`PosMAP`、
`PosUSERLIST`、`PosCHAT`、`PosMINIMAP`、`PosSHOP`、`PosNOTICE`、
`PosMAPLECHAT`、`PosCHANNEL`、`PosJOYPAD`、`PosEVENT`、`PosKEYCONFIG`、
`PosOPTIONMENU`、`PosCHARINFO`

### 网络调试
- `get_show_packets()` — 是否在控制台显示收发数据包（默认 `false`）

### 聊天
- `ChatViewMax` — 聊天栏是否最大化
- `ChatViewX` / `ChatViewY` — 用户自定义聊天栏大小
- `show_weekly` — 是否显示 Maple Chat 每周之星

### 其他
- `rightclicksell` — 右键出售功能开关
- `start_shown` — 是否已显示启动画面
- `admin` — 当前账户是否为管理员
- `caps_lock_enabled` — 大写锁定状态
- `MiniMapType` / `MiniMapSimpleMode` / `MiniMapDefaultHelpers` — 小地图设置
- `worldid` / `channelid` — 当前选中世界/频道

### 系统信息（运行时设置）
- `MACS` / `HWID` / `MAXWIDTH` / `MAXHEIGHT` — 由 `HardwareInfo` 和 `ScreenResolution` 在启动时设置
- `VolumeSerialNumber` — 硬盘卷序列号

## Entry 类型系统

| 类型 | 基类 | 示例 |
|---|---|---|
| `BoolEntry` | `Entry` | Fullscreen、VSync、SaveLogin |
| `StringEntry` | `Entry` | ServerIP、FontPathNormal |
| `ByteEntry` | `IntegerEntry<uint8_t>` | BGMVolume、DefaultWorld |
| `ShortEntry` | `IntegerEntry<uint16_t>` | Width、Height |
| `IntEntry` | `IntegerEntry<uint32_t>` | — |
| `LongEntry` | `IntegerEntry<uint64_t>` | — |
| `PointEntry` | `Entry` | PosSTATS、PosEQINV |

通过 `Setting<T>` 模板访问配置实例，内部通过 `Configuration::settings` 的 `TypeMap<Entry>` 查找或返回默认值。

## 关键状态

| 状态 | 说明 |
|---|---|
| 已加载 | 配置文件存在且全部解析成功 |
| 使用默认值 | 配置文件缺失或某条目缺失，使用 Configuration.h 中的硬编码默认值 |
| 已保存 | 退出时写入 Settings 文件 |

## 约束

- 配置值类型固定，不支持动态类型
- 分辨率受系统硬件限制（`HardwareInfo`）
- `SaveLogin` 关闭时不会保存账号名
- 自动登录仅在 `get_auto_login()` 返回 true 时生效

## 相关模块

- [Configuration.h](../../Configuration.h) — 所有配置项定义、Entry 类型、Setting 模板
- [Configuration.cpp](../../Configuration.cpp) — load/save 实现
- [agents/io/index.md](../../agents/io/index.md) — UIOptionMenu（选项菜单 UI）
- [Util/HardwareInfo](../../Util/HardwareInfo.h) — 硬件上限检测
- [Util/ScreenResolution](../../Util/ScreenResolution.h) — 屏幕分辨率检测
