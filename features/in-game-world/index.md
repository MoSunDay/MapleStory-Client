Commit: c8a7aaeaa283edd20422e7c46209c9e8a5e2504d

# 游戏内世界交互

## 概述

进入游戏世界后的核心交互：地图探索、移动、与怪物/NPC/其他玩家互动。

## 触发者

角色选择后由服务器发送 Setfield 数据包，Stage 加载地图并进入 ACTIVE 状态。

## 地图系统

### 地图加载
- 服务器指定 `mapid` 和 `portalid`
- 调用 `Stage::load(mapid, portalid)` 加载地图数据（背景、瓦片、寻路、传送门、Mob、NPC 等）
- 切换时有过渡状态（`TRANSITION`）

### 地图渲染
地图对象按层渲染：`MapBackgrounds` → `MapTilesObjs` → 地面物体 → 前景物体

### 摄像机
跟随玩家角色（`Playable`），平滑插值

## 移动系统

### 物理（Physics）
- 基于立足点（`FootholdTree`）的地面检测
- 支持跳跃、下落、攀爬（梯子/绳索）
- 角色状态：WALK / STAND / FALL / SWIM / LADDER / ROPE / SIT

### 传送门（MapPortals）
- 地图间传送，由服务器校验
- 按键交互触发

## 战斗

### 普通攻击
- 键盘触发攻击动作
- 生成攻击判定区域（`RegularAttack`）
- 服务器验证并同步伤害

### 技能
- 从技能栏或快捷键触发
- 支持多种技能动作（`SkillAction`）和技能子弹（`SkillBullet`）
- 命中特效（`SkillHitEffect`）和伤害数字（`DamageNumber`）

### 怪物（MapMobs）
- 服务器同步位置、动画、生命值
- 死亡后掉落物品

## NPC 交互

- 接近 NPC 并按交互键 → 打开对话界面（`UINpcTalk`）
- 支持多选项对话、商店（`UIShop`）
- 服务器驱动对话状态机

## 掉落物（MapDrops）

- 怪物掉落、任务掉落
- 接近时按拾取键
- 显示掉落物名称标签

## 其他玩家（MapChars）

- 服务器同步其他玩家的位置、动画、外观
- 显示名字标签、聊天气泡

## 地图特效（MapEffect）

- 天气效果、环境光效
- 通过服务器 opcode `FIELD_EFFECT` 触发

## 关键状态

| 状态 | 说明 |
|---|---|
| INACTIVE | 尚未加载地图（登录/选角阶段） |
| TRANSITION | 地图切换过渡中 |
| ACTIVE | 正常游戏状态 |

## 边界与约束

- 移动需要服务器确认，存在网络延迟
- 传送门由服务器校验，防止作弊
- 同一地图最大角色/Mob 数量受地图配置限制

## 相关模块

- [agents/gameplay/index.md](../../agents/gameplay/index.md) — Stage、Physics、Combat、MapleMap
- [agents/net/index.md](../../agents/net/index.md) — SetfieldHandlers、AttackHandlers、MapObjectHandlers
- [agents/io/index.md](../../agents/io/index.md) — 键盘输入、UI 叠层
- [agents/character/index.md](../../agents/character/index.md) — Player、Char 基类
