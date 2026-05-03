Commit: c8a7aaeaa283edd20422e7c46209c9e8a5e2504d

# Character — 角色系统模块

## 职责

管理玩家角色状态：属性、外观、背包、技能、任务、经验。

## 边界与非目标

- **不负责** 网络收发（操作触发通过 Net/Packets，状态同步通过 Net/Handlers）
- 角色渲染委托给 `CharLook`（外观）和 `Graphics` 模块

## 关键抽象

### Char（`Char.h/.cpp`）
角色的抽象基类，继承自 `MapObject`。管理外观、动画状态、特效、聊天气泡、伤害数字。
- **状态枚举**: `WALK`/`STAND`/`FALL`/`ALERT`/`PRONE`/`SWIM`/`LADDER`/`ROPE`/`DIED`/`SIT`
- `draw()` — 绘制外观、名字标签、特效、气泡
- `update(physics)` — 更新动画与移动
- `attack()` — 切换攻击姿态/动作
- `show_damage()` / `speak()` — 显示伤害/聊天

### Player（`Player.h/.cpp`）
继承 `Char`，代表当前玩家。增加玩家特有属性：属性点（AP/SP）、经验值、技能等级、任务进度、背包操作等。

### Inventory（`Character/Inventory/`）
背包系统：
- `Inventory` — 背包核心，管理装备、消耗、设置、其他、现金 5 个分类
- `Equip` — 装备物品
- `Item` — 通用物品
- `Pet` — 宠物
- `Weapon` — 武器类型定义
- `InventoryType` / `Equipstat` — 类型与属性枚举
- `Maplestat` — 角色属性枚举（STR/DEX/INT/LUK/HP/MP 等）

### Look（`Character/Look/`）
角色视觉外观：
- `CharLook` — 组合身体、头发、脸、衣物、武器等图层
- `BodyDrawInfo` — 身体绘制参数
- `Clothing` / `Hair` / `Face` — 各部位
- `Stance` — 姿态（站立、行走、跳跃、攻击等）
- `Equipslot` — 装备槽
- `Afterimage` — 攻击残影效果
- `PetLook` — 宠物外观

### 外层文件
- `CharEntry` — 登录时服务器返回的角色条目（名称、等级、外观摘要）
- `CharStats` — 角色属性（HP/MP/STR/DEX/INT/LUK 等）
- `CharEffect` — 角色特效
- `SkillData` / `SkillEntry` — 技能数据
- `Job` — 职业枚举
- `QuestLog` — 任务日志
- `Buffs` — Buff 状态
- `ExpTable` — 经验表

## 主流程

```
Session::read() → PacketSwitch::forward()
  → PlayerHandlers::handle() 解析服务器数据
    → Player.set_stats() / Player.set_level() 更新属性
    → Inventory.modify() 更新背包
    → Char.change_look() 更新外观
```

## 依赖

- `Data/` — 静态数据（装备属性、技能定义、经验表等）
- `Graphics/` — 外观渲染
- `Gameplay/Combat/DamageNumber` — 伤害显示
- `Gameplay/MapleMap/MapObject` — 地图对象基类
- `Template/` — Singleton、EnumMap、Optional、Point 等
