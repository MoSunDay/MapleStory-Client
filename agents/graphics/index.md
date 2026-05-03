Commit: c8a7aaeaa283edd20422e7c46209c9e8a5e2504d

# Graphics — OpenGL 渲染引擎

## 职责

基于 OpenGL 的 2D 渲染引擎：纹理图集管理、精灵批处理、动画播放、文字渲染、几何绘制。

## 边界与非目标

- **不负责** 游戏逻辑（战斗、移动等）
- **不负责** UI 布局与交互（由 IO/UI 模块负责）
- 仅提供绘制接口供上层调用

## 关键抽象

### GraphicsGL（`GraphicsGL.h/.cpp`）
单例，渲染引擎核心。管理 OpenGL 上下文、纹理图集（8192×8192）、顶点缓冲、着色器程序。
- `addbitmap(bmp)` — 将位图注册到图集
- `draw(bmp, rect, color, angle)` — 绘制位图
- `createlayout()` / `drawtext()` — 文字排版与渲染
- `drawrectangle()` / `drawscreenfill()` — 几何填充
- `flush(opacity)` — 提交所有批处理 quad 到 GPU
- `lock()` / `unlock()` — 场景锁定（用于叠加绘制）

纹理管理使用四叉树（`QuadTree`）做空间分配，当空间不足时 `clear()` 清空图集重新分配。

### Texture（`Texture.h/.cpp`）
纹理资源，封装 NLNX 位图与 GraphicsGL 的交互。

### Sprite（`Sprite.h/.cpp`）
精灵，与 Animation 配合从 NX 节点加载帧序列。

### Animation（`Animation.h/.cpp`）
帧动画，从 NX 节点加载，支持循环、延迟。

### Text（`Text.h/.cpp`）
文字渲染系统，通过 FreeType 加载字体字形。支持多种字体（`Text::Font` 枚举）、对齐、格式标签（颜色等）、背景样式。

### 支撑类型
- `Color` — 颜色定义
- `Geometry` — 几何绘制辅助
- `EffectLayer` — 特效叠加层
- `DrawArgument` — 绘制变换参数（位置、缩放、角度、透明度）

## 主流程

```
GraphicsGL::draw(bmp, rect, color, angle)
  → getoffset(bmp) 获取/分配图集位置
  → 生成 Quad 加入批处理队列

GraphicsGL::flush(opacity)
  → 将 quad 队列写入 VBO
  → glDrawArrays 提交绘制
  → 清空队列
```

## 依赖

- `includes/nlnx/` — 位图数据源（nl::bitmap）
- FreeType — 字体加载
- OpenGL / GLEW — 底层渲染
- `Template/` — Singleton、Point、Rectangle、Range
- `Util/QuadTree` — 图集空间管理
- `Constants` — 屏幕尺寸常量
