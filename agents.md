Commit: cbb0fe27cf9683a12eca0a569121d8033cdc2a4d

# 仓库逻辑地图

HeavenClient (JourneyClient) 是一个从零构建的 MapleStory v83 游戏客户端，使用 C++ 开发，兼容 [HeavenMS](https://github.com/ronancpl/HeavenMS) 服务器。渲染基于 OpenGL，游戏数据支持 NX 或 WZ 格式，网络通信基于 Winsock 或 Asio。

## 模块索引

| 模块 | 路径 | 职责 |
|---|---|---|
| [Net](./agents/net/index.md) | `Net/` | 网络通信、数据包收发、加密、协议处理 |
| [Gameplay](./agents/gameplay/index.md) | `Gameplay/` | 游戏世界模拟：地图、战斗、物理、摄像机 |
| [Graphics](./agents/graphics/index.md) | `Graphics/` | OpenGL 渲染引擎：纹理、精灵、动画、文字 |
| [IO](./agents/io/index.md) | `IO/` | 窗口管理、输入处理、UI 系统 |
| [Character](./agents/character/index.md) | `Character/` | 玩家角色：属性、外观、背包、技能、任务 |

## 支撑模块

- **`Audio/`** — 音效(SFX)与背景音乐(BGM)，基于 SDL2_mixer。`Sound::init()` / `Music::init()` 分离初始化
- **`Data/`** — 静态游戏数据加载（物品、装备、技能等），依据 `USE_NX` 宏从 NX 或 WZ 文件读取
- **`Util/`** — 工具：NX/WZ 文件管理（`NxFiles`/`WzFiles`）、硬件信息、四叉树、随机数
- **`Template/`** — 通用模板：Singleton、Optional、EnumMap、Point、Rectangle、TypeMap 等
- **`includes/nlnx/`** — NoLifeNx 库，NX 格式读取与解压
- **`Configuration`** — 运行时配置持久化（`Settings` 文件），管理连接、显示、音频、账号、UI 位置等配置项

## 主循环

`WinMain()/main()` → `ms::start()` → `ms::init()` → `ms::loop()`

`loop()` 使用固定时间步长（`TIMESTEP = 8ms`）驱动 `update()` + `draw(alpha)` 循环。`running()` 条件依赖网络连接、UI 未退出、窗口未关闭三者同时为真。支持 FPS 显示（`get_show_fps()`）。

## 构建系统

- **Windows**: Visual Studio 2017 解决方案（`MapleStory.sln`），无 CMake 支持
- 通过 `#define USE_ASIO` 切换网络后端（Winsock / Asio）
- 通过 `#define USE_NX` 切换数据格式（NX / WZ）
- 日志系统：`LOG(level, message)` 宏，Debug 模式输出到 stdout，Release 模式完全优化掉

## 功能索引

→ [features/index.md](./features/index.md)
