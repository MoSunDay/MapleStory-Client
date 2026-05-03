Commit: cbb0fe27cf9683a12eca0a569121d8033cdc2a4d

# IO — 窗口、输入与 UI 系统

## 职责

管理 GLFW 窗口、键盘/鼠标输入、以及完整的游戏 UI 系统（登录界面到游戏内界面）。

## 边界与非目标

- **不负责** 游戏世界渲染（由 Gameplay 模块的 Stage 负责）
- 仅处理 UI 层的绘制、更新、交互

## 关键抽象

### Window（`Window.h/.cpp`）
单例，GLFW 窗口管理。`init()` 创建窗口与 OpenGL 上下文，`begin()`/`end()` 包裹帧绘制，`check_events()` 轮询输入事件，`fadeout()` 实现淡出过渡。支持全屏切换(`toggle_fullscreen`)和剪贴板操作。

### Keyboard（`Keyboard.h/.cpp`）
键盘输入管理，映射键位到游戏动作，支持键位绑定（`add_keymapping`）。

### Cursor（`Cursor.h/.cpp`）
鼠标光标管理，支持多种光标样式，跟踪位置与按下状态。

### UI（`UI.h/.cpp`）
单例，UI 系统根节点。管理三个状态：`LOGIN`、`GAME`、`CASHSHOP`。`draw(alpha)` 和 `update()` 驱动所有 UI 元素。提供 `emplace<T>(Args...)` 模板方法创建 UI 元素，`remove(Type)` 移除，以及工具提示、拖拽图标、文本聚焦等协调功能。

```cpp
template <class T, typename...Args>
Optional<T> emplace(Args&& ...args)
{
    if (auto iter = state->pre_add(T::TYPE, T::TOGGLED, T::FOCUSED))
        (*iter).second = std::make_unique<T>(std::forward<Args>(args)...);
    return state->get(T::TYPE);
}
```

### UIState（`UIState.h/.cpp`）
UI 状态机，管理 UI 元素的增删、焦点、覆盖关系。`LOGIN`、`GAME` 和 `CASHSHOP` 各自有不同的 UI 元素集合。

### UIElement（`UIElement.h`）
所有 UI 元素的抽象基类，定义 `draw()` / `update()` / `Type` / `TOGGLED` / `FOCUSED` 等接口。

### Components（`IO/Components/`）
可复用的 UI 控件库：
- `Textfield` — 文本输入框
- `TextfieldNumeric` — 数字输入框
- `TextfieldPassword` — 密码输入框
- `Slider` — 滑块
- `Gauge` — 进度条/血条
- `Checkbox` — 复选框
- `ComboBox` — 下拉选择
- `ChatBalloon` — 聊天气泡
- `Tooltip` — 工具提示
- `Icon` — 图标（可拖拽）
- `ScrollingNotice` — 滚动公告
- `MapButton` — 地图操作按钮

### UITypes（`IO/UITypes/`）
完整 UI 界面（约 36 个 .h/.cpp 对）：
- **登录流程**: `UILogo` → `UILogin` → `UILoginNotice` → `UILoginWait` → `UITermsOfService` → `UIGender` → `UIRaceSelect` → `UICharSelect`
- **角色创建**: `UIExplorerCreation`、`UIAranCreation`、`UICygnusCreation`、`UICommonCreation`
- **游戏内**: `UIStatusBar`、`UIStatsInfo`、`UIEquipInventory`、`UIItemInventory`、`UISkillBook`、`UIQuestLog`、`UIMiniMap`、`UIWorldMap`、`UIChat`、`UIChatBar`、`UIUserList`、`UIBuffList`、`UIShop`、`UINpcTalk`、`UIWorldSelect`、`UIChannel`、`UIOptionMenu`、`UIKeyConfig`、`UIJoypad`、`UIEvent`、`UINotice`、`UIQuit`、`UISoftKey`、`UIStatusMessenger`、`UIRegion`、`UICharInfo`
- **商城**: `UICashShop`

## 主流程

```
主循环 → UI::get().update()
  → 处理键盘/鼠标事件分发
  → 各 UI 元素 update()

主循环 → UI::get().draw(alpha)
  → 按 z-order 绘制所有活跃 UI 元素
```

## 日志

UI 模块使用 `LOG(LOG_UI, ...)` 宏输出调试信息。

## 依赖

- `Graphics/` — 所有 UI 渲染
- `Net/` — 登录、背包、技能等网络操作通过 handler 回调
- `Character/` — 读取角色数据（装备、属性、技能等）以渲染 UI
- `Configuration` — 读取/保存 UI 位置、音量、键位等设置
- `Template/` — Singleton、Optional、Point、EnumMap、TypeMap 等
- `MapleStory.h` — LOG 宏定义
