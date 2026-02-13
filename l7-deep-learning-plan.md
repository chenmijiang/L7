# L7 深入学习计划

> L7 — AntV 团队的 WebGL 地理空间数据可视化分析引擎
> 仓库：https://github.com/antvis/L7

## 学习目标

以 L7 为案例，深入理解大型前端框架的架构设计、工程化方案、设计思想和各包的功能逻辑。

## 前置条件

- 已具备：TypeScript / 前端工程化基础
- 需补充：GIS 基础、设计模式、WebGL 概念

---

## 阶段一：前置知识 + L7 宏观认知（3-5 天）

**目标：建立必要的前置知识体系，理解 L7 在地理可视化领域的定位和核心概念。**

### 1.1 GIS 基础概念

- [x] 理解 GeoJSON 规范 — 读一遍 [RFC 7946](https://tools.ietf.org/html/rfc7946)，重点关注 Feature / FeatureCollection / Geometry（Point、LineString、Polygon、MultiPolygon）
- [x] 理解坐标系与投影 — WGS84 经纬度 vs 墨卡托投影（Web Mercator EPSG:3857），为什么地图都要做投影
- [x] 理解瓦片地图原理 — XYZ 瓦片分层机制、矢量瓦片 vs 栅格瓦片的区别、瓦片金字塔
- [x] 阅读 `packages/source/src/` 目录，感受 L7 如何解析这些地理数据格式

### 1.2 设计模式补充

- [x] 依赖注入（DI）— 理解 IoC 容器概念，对照 `packages/core/src/services/` 中的服务注册方式
- [x] 观察者模式 — 对照 L7 中的事件系统理解
- [x] 工厂模式 — 对照 Layer/Model 的创建流程理解

### 1.3 WebGL 概念性理解

- [x] 渲染管线概念：顶点数据 → 顶点着色器 → 光栅化 → 片元着色器 → 帧缓冲
- [x] Buffer / Texture / Framebuffer 是什么，对照 `packages/renderer/` 理解
- [x] 浏览一个 L7 shader 文件（如 `packages/layers/src/point/shaders/`），感受 GLSL 基本语法

### 1.4 L7 宏观认知

- [ ] 阅读项目 README 和官方文档首页，理解 L7 的定位：WebGL 地理空间数据可视化分析引擎
- [ ] 运行 `pnpm dev`，跑通 examples，体验 L7 的能力边界
- [ ] 梳理核心概念：Scene（场景）→ Map（底图）→ Layer（图层）→ Source（数据源）
- [ ] 理解 L7 的使用范式：声明式链式 API（`.source().shape().size().color()`）

---

## 阶段二：工程化架构（2-3 天）

**目标：理解 L7 的 monorepo 组织方式、构建体系、代码规范和发布流程。**

### 2.1 Monorepo 组织

- [ ] 阅读 `pnpm-workspace.yaml`，理解 workspace 范围
- [ ] 阅读根 `package.json`，梳理所有 scripts 的用途
- [ ] 画出 13 个包的依赖关系图（谁依赖谁），重点关注 `@antv/l7` 作为聚合包的角色
- [ ] 理解 `workspace:*` 协议在包间引用中的作用

### 2.2 构建体系

- [ ] 阅读各包的 `.fatherrc.ts` 配置，理解 Father 构建工具的配置方式
- [ ] 理解构建产物：`es/`（ESM）、`lib/`（CJS）、`dist/`（UMD）的用途和区别
- [ ] 关注 GLSL shader 的构建处理 — L7 如何把 `.glsl` 文件编译为 JS 模块
- [ ] 阅读 `tsconfig.json` 和各包的 TS 配置，理解 TypeScript 项目引用的使用方式

### 2.3 代码规范与质量

- [ ] 阅读 `CODE_GUIDELINES.md`，理解命名约定、文件组织、类型规范
- [ ] 阅读 ESLint + Prettier + Stylelint 的配置，理解代码风格强制方案
- [ ] 阅读 `commitlint` 配置，理解 Angular 风格的 commit 规范
- [ ] 理解 Husky + lint-staged 的 pre-commit 工作流

### 2.4 测试与 CI/CD

- [ ] 阅读 `jest.config.ts`，理解测试配置（jsdom 环境、canvas mock、GLSL transform）
- [ ] 理解为什么 `maxWorkers: 1`（WebGL 上下文冲突）
- [ ] 阅读 `.github/workflows/`，理解 CI 流水线、文档部署、自动发布的流程
- [ ] 理解 Changeset 版本管理方案

---

## 阶段三：Core 核心设计（4-5 天）

**目标：深入理解 L7 的核心引擎设计 — 依赖注入容器、服务体系、生命周期管理和事件通信。**

### 3.1 DI 容器设计

- [ ] 阅读 `packages/core/src/services/` 目录结构，理解服务注册和获取的机制
- [ ] 分析 `createSceneContainer()` 工厂函数 — 如何引导整个服务体系的创建
- [ ] 对比 Inversify 等主流 DI 框架，理解 L7 为什么选择手动实现轻量 DI
- [ ] 画出服务注册流程图：容器创建 → 服务绑定 → 服务获取

### 3.2 核心服务体系（15+ 服务）

- [ ] **SceneService** — 场景生命周期管理，阅读其完整实现，理解初始化和销毁流程
- [ ] **LayerService** — 图层注册、管理、渲染调度，理解 Layer 的添加/移除/更新机制
- [ ] **MapService** — 底图抽象层，理解如何对 Mapbox/AMap/MapLibre 做统一抽象
- [ ] **CameraService** — 相机和视图管理，理解视口变换
- [ ] **RendererService** — 渲染器抽象，理解 device/regl 两套渲染后端的切换
- [ ] **InteractionService** — 用户交互处理（缩放、拖拽、点击）
- [ ] **PickingService** — 对象拾取/选择，理解颜色编码拾取的原理
- [ ] **CoordinateSystemService** — 坐标系变换，经纬度 ↔ 屏幕坐标 ↔ WebGL 坐标
- [ ] **IconService / FontService** — 图标和字体资源管理
- [ ] 画一张核心服务关系图：哪些服务依赖哪些服务

### 3.3 生命周期管理

- [ ] 梳理 Scene 的完整生命周期：`new Scene()` → `init` → `bindMap` → `bindRenderer` → `bindLayers` → `render` → `destroy`
- [ ] 梳理 Layer 的生命周期：`init` → `bindModels` → `bindData` → `bindEncoding` → `bindRender` → `update` → `destroy`
- [ ] 理解 Scene 和 Layer 生命周期的协调关系
- [ ] 分析异步初始化的处理方式（底图异步加载、数据异步获取）

### 3.4 事件系统与通信

- [ ] 分析事件总线的实现方式
- [ ] 理解服务间如何通过事件解耦（如 Map 变化通知 Layer 重绘）
- [ ] 分析 Layer 的交互事件（click、mousemove 等）从 DOM → 拾取 → 回调的完整链路

---

## 阶段四：各包功能逻辑（5-7 天）

**目标：逐包分析各包的职责边界、内部设计和包间协作关系。**

### 4.1 Scene 包（`@antv/l7-scene`）

- [ ] 阅读 Scene 类的完整实现，理解它作为"门面"的设计（Facade 模式）
- [ ] 分析 Scene 如何编排 Core 中各服务的初始化顺序
- [ ] 理解 Scene 暴露的公共 API 与内部服务的映射关系
- [ ] 分析 Scene 如何管理多个 Layer 的渲染顺序和更新

### 4.2 Maps 包（`@antv/l7-maps`）

- [ ] 理解地图适配器模式 — 统一的 IMapService 接口
- [ ] 对比 Mapbox / AMap / MapLibre 三种适配器的实现差异
- [ ] 分析事件代理：底图事件如何转换为 L7 内部事件
- [ ] 理解 Map 包是唯一与外部地图 SDK 直接交互的包（隔离外部依赖）

### 4.3 Source 包（`@antv/l7-source`）

- [ ] 分析数据解析管线：原始数据 → Parser → Transform → 标准化数据
- [ ] 理解支持的数据格式：GeoJSON、CSV、JSON、Image、Raster、MVT
- [ ] 分析 Transform 操作：聚合（aggregate）、聚类（cluster）、网格化（grid/hexagon）
- [ ] 理解 Source 如何将地理数据转换为 Layer 可消费的标准格式
- [ ] 分析矢量瓦片数据源的实现

### 4.4 Layers 包（`@antv/l7-layers`）

- [ ] 分析 BaseLayer 抽象类 — 所有图层的公共逻辑（数据绑定、样式映射、渲染调度）
- [ ] 理解 Layer → Model → Shader 三级抽象：Layer 是业务层，Model 是渲染模型，Shader 是 GPU 程序
- [ ] 以 PointLayer 为例做完整追踪：`.source(data).shape('circle').size(10).color('red')` 每一步发生了什么
- [ ] 分析视觉通道映射（Visual Encoding）：数据字段如何映射到 size、color、shape
- [ ] 对比不同 Layer 类型的 Model 实现差异（PointLayer vs LineLayer vs PolygonLayer）
- [ ] 分析 TileLayer 的特殊设计 — 瓦片按需加载和回收

### 4.5 Renderer 包（`@antv/l7-renderer`）

- [ ] 理解双渲染后端设计：Device Renderer（@antv/g-device-api）vs Regl Renderer
- [ ] 分析 RendererService 接口 — 上层代码如何不感知具体渲染后端
- [ ] 理解渲染资源管理：Buffer/Texture/Program 的创建和销毁

### 4.6 Component 包（`@antv/l7-component`）

- [ ] 分析 Marker/Popup 的实现 — DOM 元素与地图坐标的绑定
- [ ] 分析 Control 体系 — Zoom、Scale、Logo 等控件的注册和管理
- [ ] 理解 Component 如何通过 Service 与 Scene 协作

### 4.7 包间协作全景

- [ ] 画出完整的数据流图：用户数据 → Source 解析 → Layer 映射 → Model 构建 → Renderer 绘制 → 屏幕
- [ ] 画出完整的交互流图：用户点击 → DOM 事件 → InteractionService → PickingService → Layer 回调
- [ ] 总结各包的职责边界和依赖方向，验证是否遵循单向依赖原则

---

## 阶段五：渲染管线与设计思想总结（3-4 天）

**目标：理解 L7 的 WebGL 渲染流程，并从全局视角总结设计思想和架构模式。**

### 5.1 渲染管线（概念性理解为主）

- [ ] 梳理一帧的完整渲染流程：Scene.render() → 遍历 Layers → 每个 Layer 调用 Model.bindRender → GPU 执行
- [ ] 理解多 Layer 渲染的排序策略（zIndex、透明度混合）
- [ ] 分析 PickingService 的颜色编码拾取：为每个要素分配唯一颜色 → 离屏渲染 → 读取像素 → 反查要素

### 5.2 Shader 体系

- [ ] 理解 Shader 文件的组织方式：`shaders/` 目录下的 `.vert` / `.frag` 文件
- [ ] 分析一个完整的 Shader 示例（如 PointLayer 的 circle shader），理解 uniform / attribute / varying 的数据流
- [ ] 理解 L7 如何通过 Shader 注入实现样式映射（颜色、大小映射到 GLSL 中）

### 5.3 性能优化策略

- [ ] 分析数据分块与瓦片按需加载（TileLayer）
- [ ] 理解 GPU 实例化渲染（Instanced Rendering）在 PointLayer 中的应用
- [ ] 分析脏标记（Dirty Flag）机制 — 避免不必要的重绘
- [ ] 理解 Texture Atlas 在图标/字体渲染中的应用

### 5.4 设计思想总结

- [ ] 总结 L7 的分层架构：数据层（Source）→ 映射层（Layer）→ 渲染层（Renderer）→ 展示层（Map）
- [ ] 总结 L7 的核心设计模式使用：DI、Facade、Adapter、Strategy、Observer、Factory
- [ ] 对比 L7 与同类框架（Deck.gl、Kepler.gl）的架构差异
- [ ] 写一篇个人总结：L7 的架构设计有哪些值得借鉴的地方，有哪些可改进之处

---

## 时间线概览

| 阶段     | 主题                | 预计天数     | 关键产出                     |
| -------- | ------------------- | ------------ | ---------------------------- |
| 一       | 前置知识 + 宏观认知 | 3-5 天       | 跑通 demo，建立概念模型      |
| 二       | 工程化架构          | 2-3 天       | 包依赖关系图，理解构建流程   |
| 三       | Core 核心设计       | 4-5 天       | 服务关系图，生命周期流程图   |
| 四       | 各包功能逻辑        | 5-7 天       | 数据流图，交互流图，逐包笔记 |
| 五       | 渲染管线与总结      | 3-4 天       | 架构总结文档                 |
| **总计** |                     | **17-24 天** |                              |

## 学习建议

1. **边读边画图** — 每个阶段都建议画架构图、流程图，加深理解
2. **以 PointLayer 为主线** — 它是最典型的 Layer，适合作为贯穿全流程的学习主线
3. **先面后点** — 先理解整体架构，再深入单个模块的实现细节
4. **对照代码和文档** — L7 的官方文档和源码注释都比较完善，交叉参考效果最好
