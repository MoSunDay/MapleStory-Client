Commit: a359863eb8b98abe97bcfc1f3dadc397f5e03f2d

# IO — 窗口、输入与 UI 系统

## 职责

管理 GLFW 窗口、键盘/鼠标输入、以及完整的游戏 UI 系统（登录界面到游戏内界面，含现金商城界面）。

## 边界与非目标

- **不负责** 游戏世界渲染（由 Gameplay 模块的 Stage 负责）
- 仅处理 UI 层的绘制、更新、交互

## 关键抽象

### Window（`Window.h/.cpp`）
单例，GLFW 窗口管理。`init()` 创建窗口与 OpenGL 上下文（通过 glad 加载 OpenGL），`begin()`/`end()` 包裹帧绘制，`check_events()` 轮询输入事件，`fadeout()` 实现淡出过渡。支持全屏切换(`toggle_fullscreen`)和剪贴板操作。

### Keyboard（`Keyboard.h/.cpp`）
键盘输入管理，映射键位到游戏动作，支持键位绑定（`add_keymapping`）。

### Cursor（`Cursor.h/.cpp`）
鼠标光标管理，支持多种光标样式，跟踪位置与按下状态。

### UI（`UI.h/.cpp`）
单例，UI 系统根节点。管理**三个状态**：`LOGIN`、`GAME`、**`CASHSHOP`**（mac 分支仅有 LOGIN/GAME 两个状态）。`draw(alpha)` 和 `update()` 驱动所有 UI 元素。提供 `emplace<T>()` 模板方法创建 UI 元素，`remove(Type)` 移除，以及工具提示、拖拽图标、文本聚焦等协调功能。

### UIState（`UIState.h`）
UI 状态接口抽象基类，定义 `draw()` / `update()` / `send_cursor()` / `send_key()` / `pre_add()` / `remove()` / `get()` 等虚函数。`UIStateNull` 为空实现（用于初始化前状态）。

### 状态实现

- **UIStateLogin**（`UIStateLogin.h/.cpp`）— 登录流程 UI 元素集合
- **UIStateGame**（`UIStateGame.h/.cpp`）— 游戏内 UI 元素集合
- **UIStateCashShop**（`UIStateCashShop.h/.cpp`）— 现金商城 UI 元素集合（**linux 分支特有**）

### UIElement（`UIElement.h/.cpp`）
所有 UI 元素的抽象基类，定义 `draw()` / `update()` / `Type` / `TOGGLED` / `FOCUSED` 等接口。

### Components（`IO/Components/`）
可复用的 UI 控件库（约 20 对 h/cpp）：
- `Textfield` — 文本输入框
- `Button` / `AreaButton` / `MapleButton` / `TwoSpriteButton` — 各类型按钮
- `Slider` — 滑块
- `Gauge` — 进度条/血条
- `MapleComboBox` — 下拉选择
- `ChatBalloon` — 聊天气泡
- `Tooltip` / `EquipTooltip` / `ItemTooltip` / `SkillTooltip` / `MapTooltip` / `TextTooltip` — 各类工具提示
- `Icon` / `IconCover` / `StatefulIcon` — 图标
- `ScrollingNotice` — 滚动公告
- `NpcText` — NPC 文本
- `NameTag` — 名字标签
- `MapleFrame` — 框架
- `Charset` — 字符集

### UITypes（`IO/UITypes/`）
完整 UI 界面（共 **38 个** .h/.cpp 对，**比 mac 多 UICashShop 和 UICharInfo**）：

- **登录流程**: `UILogo` → `UILogin` → `UILoginNotice` → `UILoginwait` → `UITermsOfService` → `UIGender` → `UIRaceSelect` → `UICharSelect`
- **角色创建**: `UIExplorerCreation`、`UIAranCreation`、`UICygnusCreation`
- **游戏内**: `UIStatusbar`、`UIStatsinfo`、`UIEquipInventory`、`UIItemInventory`、`UISkillbook`、`UIQuestLog`、`UIMiniMap`、`UIWorldMap`、`UIChat`、`UIChatbar`、`UIUserList`、`UIBuffList`、`UIShop`、`UINpcTalk`、`UIWorldSelect`、`UIChannel`、`UIOptionMenu`、`UIKeyConfig`、`UIJoypad`、`UIEvent`、`UINotice`、`UIQuit`、`UISoftkey`、`UIStatusMessenger`、`UIRegion`、**`UICharInfo`**
- **现金商城**: **`UICashShop`**

## 主流程

```
主循环 → UI::get().update()
  → 处理键盘/鼠标事件分发
  → 各 UI 元素 update()

主循环 → UI::get().draw(alpha)
  → 按 z-order 绘制所有活跃 UI 元素
```

状态切换：`UI::change_state(State state)` → 创建对应的 `UIStateLogin`/`UIStateGame`/`UIStateCashShop` 实例

## 依赖

- `Graphics/` — 所有 UI 渲染（通过 glad OpenGL）
- `Net/` — 登录、背包、技能等网络操作通过 handler 回调
- `Character/` — 读取角色数据（装备、属性、技能等）以渲染 UI
- `Configuration` — 读取/保存 UI 位置、音量、键位等设置（含 `PosCHARINFO`）
- `Template/` — Singleton、Optional、Point、EnumMap、TypeMap 等
