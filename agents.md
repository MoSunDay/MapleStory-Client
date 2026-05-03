Commit: a359863eb8b98abe97bcfc1f3dadc397f5e03f2d

# 仓库逻辑地图

HeavenClient 是一个从零构建的 MapleStory v83 游戏客户端，使用 C++17 开发，兼容 [HeavenMS](https://github.com/ronancpl/HeavenMS) 服务器。基于 OpenGL 渲染，游戏数据从 NX 文件加载，网络通信基于 Asio。

本分支为 Linux 目标平台适配版本，使用 CMake 构建，依赖库以静态/动态库形式打包在 `libs/` 目录中。

## 与 macOS 分支的主要差异

- **OpenGL 加载**：使用 **glad**（而非 GLEW）
- **音频**：使用 **OpenAL + alure**（而非 SDL2_mixer），`Music::update_context()` 在主循环 update 中调用
- **构建**：所有第三方库（glad、glfw、openal-soft、alure、lz4、NoLifeNx、asio、stb）均位于 `libs/` 目录
- **Net/Packets**：无 `CustomPackets.h`（无自定义协议扩展）；增加 `PlayerInteractionPackets.h`（玩家交互协议）
- **Net/Handlers**：增加 `CashShopHandlers`、`PlayerInteractionHandlers`、`TestingHandlers`
- **IO/UI**：增加 `UIStateCashShop` 状态类，`UI` 类有三个状态（`LOGIN`、`GAME`、`CASHSHOP`）
- **IO/UITypes**：增加 `UICashShop`、`UICharInfo`；具备 `UISoftKey`、`UIStatusMessenger`
- 无 `#define PRINT_WARNINGS`（mac 分支有此编译宏）

## 模块索引

| 模块 | 路径 | 职责 |
|---|---|---|
| [Net](./agents/net/index.md) | `Net/` | 网络通信、数据包收发、加密、协议处理 |
| [Gameplay](./agents/gameplay/index.md) | `Gameplay/` | 游戏世界模拟：地图、战斗、物理、摄像机 |
| [Graphics](./agents/graphics/index.md) | `Graphics/` | OpenGL 渲染引擎：纹理、精灵、动画、文字 |
| [IO](./agents/io/index.md) | `IO/` | 窗口管理、输入处理、UI 系统 |
| [Character](./agents/character/index.md) | `Character/` | 玩家角色：属性、外观、背包、技能、任务 |

## 支撑模块

- **`Audio/`** — 音效(SFX)与背景音乐(BGM)，基于 **OpenAL + alure**；音频文件从 NX 节点加载到内存缓冲区后由 alure 解码播放
- **`Data/`** — 静态游戏数据加载（物品、装备、技能等），从 NX 文件读取
- **`Util/`** — 工具：NX 文件管理、硬件信息、四叉树、随机数、哈希
- **`Template/`** — 通用模板：Singleton、Optional、EnumMap、Point、Rectangle、Cache 等
- **`libs/NoLifeNx/`** — NoLifeNx 库，NX 格式读取与解压
- **`libs/`** — 所有第三方依赖（glad、glfw、openal-soft、alure、lz4、asio、stb）
- **`Configuration`** — 运行时配置持久化（`Settings` 文件），约 35 项配置条目，含 `UICharInfo` 位置配置（`PosCHARINFO`）

## 主循环

`main()` → `ms::start()` → `ms::init()` → `ms::loop()`

`loop()` 使用固定时间步长（`TIMESTEP = 8ms`）驱动 `update()` + `draw(alpha)` 循环。

`update()` 调用顺序：`Window::check_events()` → `Window::update()` → `Stage::update()` → `UI::update()` → `Session::read()` → **`Music::update_context()`**

`running()` 条件依赖网络连接（`Session::is_connected()`）、UI 未退出（`UI::not_quitted()`）、窗口未关闭（`Window::not_closed()`）三者同时为真。

## 构建系统

- **Linux**: CMake 3.14+，项目名 `HeavenClient`，C++17 标准
- 第三方库均从 `libs/` 目录编译/链接：`libglad.a`、`libglfw3.a`、`libopenal.so`、`libalure2.so`、`liblz4.a`、`libNoLifeNx.a`
- 链接系统库：`GL`、`X11`、`dl`、`pthread`
- FreeType 通过 CMake `FindFreetype` 查找或从 `libs/` 回退

## 功能索引

→ [features/index.md](./features/index.md)
