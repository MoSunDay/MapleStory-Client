Commit: c8a7aaeaa283edd20422e7c46209c9e8a5e2504d

# 仓库逻辑地图

HeavenClient (JourneyClient) 是一个从零构建的 MapleStory v83 游戏客户端，使用 C++17 开发，兼容 [HeavenMS](https://github.com/ronancpl/HeavenMS) 服务器。渲染基于 OpenGL，游戏数据从 NX 文件加载，网络通信基于 Asio。

## 模块索引

| 模块 | 路径 | 职责 |
|---|---|---|
| [Net](./agents/net/index.md) | `Net/` | 网络通信、数据包收发、加密、协议处理 |
| [Gameplay](./agents/gameplay/index.md) | `Gameplay/` | 游戏世界模拟：地图、战斗、物理、摄像机 |
| [Graphics](./agents/graphics/index.md) | `Graphics/` | OpenGL 渲染引擎：纹理、精灵、动画、文字 |
| [IO](./agents/io/index.md) | `IO/` | 窗口管理、输入处理、UI 系统 |
| [Character](./agents/character/index.md) | `Character/` | 玩家角色：属性、外观、背包、技能、任务 |

## 支撑模块

- **`Audio/`** — 音效(SFX)与背景音乐(BGM)，基于 SDL2_mixer
- **`Data/`** — 静态游戏数据加载（物品、装备、技能等），从 NX 文件读取
- **`Util/`** — 工具：NX 文件管理、硬件信息、四叉树、随机数、哈希
- **`Template/`** — 通用模板：Singleton、Optional、EnumMap、Point、Rectangle、Cache 等
- **`includes/nlnx/`** — NoLifeNx 库，NX 格式读取与解压
- **`Configuration`** — 运行时配置持久化（`Settings` 文件），约 35 项配置条目

## 主循环

`main()` → `ms::start()` → `ms::init()` → `ms::loop()`

`loop()` 使用固定时间步长（`TIMESTEP = 8ms`）驱动 `update()` + `draw(alpha)` 循环。`running()` 条件依赖网络连接、UI 未退出、窗口未关闭三者同时为真。

## 构建系统

- **macOS/Linux**: CMake 3.16+，依赖 Homebrew (`/opt/homebrew`) 安装的 OpenGL、GLFW、GLEW、FreeType、LZ4、Asio
- **Windows**: Visual Studio 2017 解决方案

## 功能索引

→ [features/index.md](./features/index.md)
