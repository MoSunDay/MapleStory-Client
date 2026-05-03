Commit: c8a7aaeaa283edd20422e7c46209c9e8a5e2504d

# 角色管理

## 概述

玩家角色的属性、背包、装备、技能、任务等数据管理。所有变更通过服务器同步。

## 触发者

- 游戏内 UI 操作（打开背包、装备切换、技能加点等）
- 服务器主动推送（获得物品、升级等）

## 属性系统

### 基础属性（CharStats）
- HP、MP、经验值（EXP）
- STR / DEX / INT / LUK ⸺ 装备需求与伤害计算
- AP（升级获得属性点）、SP（技能点）

### 装备属性
- 装备（Equip）可提供属性加成
- 套装效果通过服务器计算

### 视觉属性
- 外观（CharLook）由装备、头发、脸型等组成
- 装备变更自动更新外观

## 背包系统（Inventory）

### 分类（InventoryType）
- **EQUIP**（装备）— 武器、防具、饰品
- **USE**（消耗品）— 药水、卷轴、飞镖
- **SETUP**（设置）— 椅子、天气道具
- **ETC**（其他）— 任务道具、素材
- **CASH**（现金道具）

### 操作
- 移动物品（内部/装/卸/MOVE_INTERNAL/MOVE_EQUIP/MOVE_UNEQUIP）
- 增删、数量变更、交换
- 通过 `Inventory.modify()` 统一入口

### 查询
- 查找空闲槽位、特定物品位置
- 获取总属性（`totalstats`）

## 技能系统

- 技能书（`UISkillbook`）展示已学技能
- 技能等级、冷却时间
- 快捷键绑定

## 任务系统

- 任务日志（`UIQuestLog`）显示进行中/已完成任务
- 任务进度由服务器同步

## 关键状态

| 状态 | 说明 |
|---|---|
| 背包已加载 | 服务器发送完所有物品数据 |
| 装备已应用 | 装备属性计入总属性 |
| 技能已同步 | 技能列表与等级已获取 |

## 约束

- 所有背包操作需服务器确认
- 装备等级/属性需求在客户端校验
- 任务进度仅服务器为权威源

## 相关模块

- [agents/character/index.md](../../agents/character/index.md) — Inventory、Look、Skills、Quests
- [agents/net/index.md](../../agents/net/index.md) — InventoryHandlers、PlayerHandlers、InventoryPackets
- [agents/io/index.md](../../agents/io/index.md) — UIItemInventory、UIEquipInventory、UISkillbook、UIStatsinfo、UIQuestLog
- [Data/](../../Data/) — 静态物品/装备/技能数据
