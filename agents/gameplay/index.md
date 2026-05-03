Commit: c8a7aaeaa283edd20422e7c46209c9e8a5e2504d

# Gameplay — 游戏世界模拟模块

## 职责

管理游戏世界的模拟：地图加载与渲染、物理碰撞、战斗逻辑、摄像机跟随。

## 边界与非目标

- **不负责** 网络数据包收发（由 Net 模块负责）
- **不负责** UI 渲染（由 IO 模块负责）
- Stage 是游戏世界的主入口，由主循环 `update()`/`draw()` 调用

## 关键抽象

### Stage（`Stage.h/.cpp`）
单例，游戏世界根容器。管理三个状态：`INACTIVE` → `TRANSITION` → `ACTIVE`。
- `load(mapid, portalid)` — 加载地图
- `clear()` — 清除地图所有对象
- `loadplayer(CharEntry)` — 从角色数据构建 Player
- `update()` / `draw(alpha)` — 逐帧更新/绘制所有子对象
- 持有 `Camera`、`Physics`、`Player`、`Combat` 及各地图子对象

### Camera（`Camera.h/.cpp`）
摄像机系统，跟随 `Playable` 对象，平滑滚动。

### Physics（`Physics/`）
碰撞检测与移动物理。
- `FootholdTree` — 立足点树，用于地面检测与平台跳跃
- `PhysicsObject` — 物理对象基类，提供位置、速度、地面状态

### Combat（`Gameplay/Combat/`）
战斗系统：攻击、技能、伤害数字、子弹。
- `Attack` — 攻击抽象基类
- `RegularAttack` — 普通攻击
- `Skill` — 技能攻击
- `SkillAction` — 技能动作定义
- `SkillBullet` — 技能子弹/弹道
- `SkillHitEffect` — 技能命中特效
- `Bullet` — 投射物
- `DamageNumber` — 伤害数字显示

### MapleMap（`Gameplay/MapleMap/`）
地图对象管理：
- `MapInfo` — 地图元数据
- `MapTilesObjs` — 地图瓦片与对象
- `MapBackgrounds` — 背景渲染
- `MapPortals` — 传送门
- `MapMobs` / `MapNpcs` / `MapChars` / `MapDrops` — 地图上的 Mob、NPC、其他玩家角色、掉落物
- `MapReactors` — 反应器（可交互地图元素）
- `MapEffect` — 地图特效（如天气、环境光效）

## 主流程

```
Stage::update()
  → check_events() 处理输入
  → physics 更新所有物理对象
  → mobs.update() / npcs.update() / chars.update() ...
  → check_portals() / check_seats() / check_ladders() / check_drops()
  → camera.update()

Stage::draw(alpha)
  → backgrounds.draw()
  → tilesobjs.draw()
  → 各对象按层排序绘制
  → foreground 绘制
  → UI 由 IO 模块独立绘制
```

## 依赖

- `Character/` — Player、Char、CharLook 等
- `Graphics/` — 所有渲染调用
- `IO/` — 键盘/鼠标输入状态（`KeyType`、`Cursor::State`）
- `Net/` — 网络数据推动地图对象状态变更（通过 Handlers 调用 Stage 方法）
- `Template/` — Singleton、TimedQueue、Point、Rectangle 等
