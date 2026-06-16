# crpoc / c.html 项目说明

C语言数据结构可视化学习页，单文件 HTML，所有逻辑内联。

---

## 页面结构

```
<nav>          导航栏
<div#home>     首页标题
<section#basics>  基础知识讲解（6 张 b-card）
<div#demo>     代码演示区
  .chapter-tabs   章节切换按钮
  #chapter-tree   树
  #chapter-heap   堆
<footer>
```

---

## 演示区章节与 Tab

### 树 `#chapter-tree`
8 个 tab，共用 `#chapter-tree .demo-wrap / .tab-btn` 选择器：

| Tab ID | 按钮文字 | 切换函数 |
|---|---|---|
| `tab-pre` | 先序 | `switchTab('pre', btn)` |
| `tab-in` | 中序 | `switchTab('in', btn)` |
| `tab-post` | 后序 | `switchTab('post', btn)` |
| `tab-level` | 层序 | `switchTab('level', btn)` |
| `tab2-avl-ins` | AVL 插入 | `switchTree2Tab('avl-ins', btn)` |
| `tab2-avl-del` | AVL 删除 | `switchTree2Tab('avl-del', btn)` |
| `tab2-rb-ins` | 红黑树 插入 | `switchTree2Tab('rb-ins', btn)` |
| `tab2-rb-del` | 红黑树 删除 | `switchTree2Tab('rb-del', btn)` |

### 堆 `#chapter-heap`
4 个 tab：入堆 / 出堆 / 建堆 / Top-K，函数 `switchHeapTab(type, btn)`。

---

## demo-wrap 两栏布局

```
.demo-main  (grid: 1fr 340px)
├── .demo-left
│   ├── [.case-sel]   可选：情形切换按钮（AVL/RB 用）
│   ├── .tree-area    SVG 可视化
│   ├── [.harr]       堆专用数组显示
│   └── .controls     ▶开始/重置  单步  自动  速度滑块
└── .demo-right
    ├── .algo-code    核心代码
    ├── .legend       图例
    └── .log-area     操作日志（只显示当前步骤）
```

---

## JavaScript 模块

### 遍历模块（先/中/后/层序）
- 树结构：`const TREE = { nodes, edges, left, right }`
- 步骤生成：`buildPreSteps / buildInSteps / buildPostSteps / buildLevelSteps`
- 渲染：`renderSVG(svgId)` + `setNode(svgId, id, state)`
- 控制：`startDemo / stepDemo / autoDemo(type)`
- Tab 切换：`switchTab(type, btn)` → 目标 `#chapter-tree`

### 堆模块
- 固定位置表：`const HPOS = [null, {x,y}, ...]`（9 个节点）
- 步骤生成：`buildHInsertSteps / buildHDeleteSteps / buildHBuildSteps / buildHTopkSteps`
- 渲染：`renderHeapSVG / renderHeapArr / renderTopkInput`
- 控制：`startHeapDemo / stepHeapDemo / autoHeapDemo(type)`
- 初始化：`initHeapChapter()` → 在首次切换到堆章节时调用

### AVL / 红黑树模块
- 通用渲染器：`renderBSTSVG(svgId, step)`
  - `step = { nodes, edges, state, msg, hl }`
  - `state` = `{ nodeId: 'active'|'stacked'|'visited'|'rb-hl'|... }`
- 布局辅助：`layoutTree(nodes, rootId, x, y, spread)` — 递归计算 x/y
- 边辅助：`buildEdges(nodes)` — 从 `node.left / node.right` 生成边数组
- 快照辅助：`_snap(nodes, rootId, state, msg, hl)` — 深拷贝 + layout + buildEdges
- 节点属性：
  - AVL：`{ id, val, left?, right?, bf }` — bf 显示在节点下方，|bf|>1 时红色
  - RB：`{ id, val, left?, right?, color:'R'|'B' }` — 节点用 `rb-red` / `rb-black` CSS 类着色
- 步骤生成：
  - `buildAVLInsSteps(cas)` — cas ∈ 'll'|'rr'|'lr'|'rl'
  - `buildAVLDelSteps()`
  - `buildRBInsSteps(cas)` — cas ∈ 'recolor'|'rotate'
  - `buildRBDelSteps(cas)` — cas ∈ 'leaf'|'replace'
- 情形选择器：`setAVLCase / setRBCase / setRBDCase(cas, btn)`
- 控制：`startT2Demo / stepT2Demo / autoT2Demo(type)`
- Tab 切换：`switchTree2Tab(type, btn)` → 目标 `#chapter-tree`（与遍历共享容器）
- 初始化：`initTree2Chapter()` → 预生成 AVL/RB 演示步骤（首次切换到对应 tab 时自动调用）

### 公共
- 速度：`updateSpeed(type, val)` → `SPEEDS[type]`，element id 为 `speed-{type}-lbl`
- 章节切换：`switchChapter(id, btn)` → 激活 `#chapter-{id}`，按需调用 `initHeapChapter / initGraphChapter`

---

## CSS 关键类

| 类 | 用途 |
|---|---|
| `.node-circle` | SVG 节点圆，基础蓝色 |
| `.node-circle.active` | 橙色（当前处理） |
| `.node-circle.stacked` | 紫色（栈中 / 旋转支点） |
| `.node-circle.visited` | 绿色（已完成） |
| `.node-circle.queued` | 青色（队列中） |
| `.node-circle.h-cmp` | 黄色（堆比较） |
| `.node-circle.h-swap` | 红色（堆交换） |
| `.node-circle.h-new` | 亮绿（堆新节点） |
| `.node-circle.rb-red` | 深红（红黑树红节点） |
| `.node-circle.rb-black` | 深蓝（红黑树黑节点） |
| `.case-btn.active` | 情形选择器激活态 |

---

## 写作偏好
- 代码量尽量少，逻辑节奏统一
- 演示用固定典型示例，不做通用动态插入
- 操作日志只显示当前步骤（`innerHTML =`，不累积）
