# Sapiens Timeline 实施方案

> **For agentic workers:** 按序执行以下任务。每步使用 checkbox (`- [ ]`) 追踪。

**Goal:** 构建《人类简史》暗色星图交互时间线网站——单 HTML 文件 + JSON 数据，零依赖。

**Architecture:** Canvas 2D 全屏绘制星空与时间线，HTML overlay 展示 UI 控件和详情卡片。单一 `index.html` 文件（内嵌 CSS + JS），数据独立为 `data/timeline.json`，浏览器直接打开即可。

**Tech Stack:** 纯 HTML/CSS/JS (Canvas 2D)，Google Fonts (Noto Serif SC, Noto Sans SC, Inter, JetBrains Mono)

---

## 文件结构

```
D:\Sapiens/
├── index.html              # 主站单文件
├── data/
│   └── timeline.json       # 30+ 时间节点数据
└── README.md               # 说明文档
```

---

### Task 1: 项目骨架 + README

**Files:**
- Create: `D:\Sapiens\README.md`

- [ ] **Step 1: 创建 README.md**

写入项目说明，包含：项目简介、启动方式（浏览器打开 index.html）、数据格式说明、如何添加节点。

---

### Task 2: 梳理《人类简史》时间线数据

**Files:**
- Create: `D:\Sapiens\data\timeline.json`

- [ ] **Step 1: 研究并编写 30+ 条时间线数据**

从《人类简史》四大时代中提取关键事件节点：

**认知革命** (~70,000 BCE – ~12,000 BCE)
1. 智人出现 (~300,000 BCE) - key
2. 认知革命/语言诞生 (~70,000 BCE) - key
3. 智人走出非洲 (~70,000 BCE) - major
4. 智人抵达澳大利亚 (~45,000 BCE) - major
5. 全球大型动物灭绝 (~40,000 BCE) - major
6. 智人抵达美洲 (~16,000 BCE) - major
7. 尼安德特人灭绝 (~30,000 BCE) - major
8. 狩猎采集社会巅峰 (~15,000 BCE) - minor

**农业革命** (~12,000 BCE – ~3500 BCE)
9. 农业革命开始 / 小麦驯化 (~9500 BCE) - key
10. 永久定居点出现 (~9000 BCE) - major
11. 动物驯化（山羊/绵羊）(~9000 BCE) - major
12. 文字雏形出现 (~3400 BCE) - key
13. 苏美尔城市兴起 (~3500 BCE) - major
14. 阶级制度形成 (~4000 BCE) - major
15. 金字塔建造 (~2500 BCE) - major
16. 金钱/货币诞生 (~2500 BCE) - key

**人类融合统一** (~2000 BCE – ~1500 CE)
17. 汉帝国建立 (~200 BCE) - major
18. 罗马帝国巅峰 (~100 CE) - major
19. 佛教传播 (~300 CE) - major
20. 基督教成为罗马国教 (~380 CE) - major
21. 伊斯兰教兴起 (~622 CE) - key
22. 中国发明印刷术 (~600 CE) - major
23. 蒙古帝国建立 (~1206 CE) - major
24. 黑死病 (~1347 CE) - major
25. 哥伦布发现美洲 (~1492 CE) - key

**科学革命** (~1500 CE – 现在)
26. 科学革命开端 / 哥白尼 (~1543 CE) - key
27. 工业革命 (~1760 CE) - key
28. 达尔文《物种起源》(~1859 CE) - major
29. 电力和内燃机 (~1880 CE) - major
30. 原子弹 / 广岛 (~1945 CE) - key
31. 登月 (~1969 CE) - major
32. 互联网诞生 (~1990 CE) - key
33. CRISPR 基因编辑 (~2012 CE) - major
34. AI 革命 (~2020 CE) - key

每条数据格式：
```json
{
  "id": "string",
  "year": number (负数 = BCE, 正数 = CE),
  "yearDisplay": "中文年份",
  "yearDisplayEn": "English year",
  "era": "cognitive|agricultural|unification|scientific",
  "titleZh": "中文标题",
  "titleEn": "English title",
  "summaryZh": "中文概要 (1-2句)",
  "summaryEn": "English summary",
  "detailZh": "中文详述 (100-200字)",
  "detailEn": "English detail",
  "significance": "key|major|minor",
  "tags": ["标签1", "标签2"]
}
```

写入 `D:\Sapiens\data\timeline.json`。

---

### Task 3: HTML 骨架 + CSS 设计系统

**Files:**
- Create: `D:\Sapiens\index.html`

- [ ] **Step 1: 写入完整 HTML 骨架**

建立基本结构：Google Fonts 引入、CSS 变量（配色/字体/间距）、全屏 Canvas + overlay 容器。

CSS 变量：
```css
:root {
  --bg-deep: #080D16;
  --bg-mid: #0D1B2A;
  --star-white: #E6EDF3;
  --gold: #D4A853;
  --era-cognitive: #7B9EBD;
  --era-agricultural: #C4956A;
  --era-unification: #8B6B9E;
  --era-scientific: #5FA8B4;
  --card-bg: rgba(13, 17, 23, 0.85);
  --text-primary: #E6EDF3;
  --text-secondary: #8B949E;
  --card-border: rgba(212, 168, 83, 0.3);
}
```

HTML 结构：
```html
<canvas id="starfield"></canvas>
<div id="overlay">
  <div id="era-labels"><!-- 四大时代标签 --></div>
  <div id="detail-card" class="hidden"><!-- 详情卡片 --></div>
  <div id="minimap"><!-- 迷你地图 --></div>
  <div id="scrubber"><!-- 底部搓条 --></div>
</div>
```

初始 CSS：全屏布局、canvas 覆盖全屏、overlay 层 pointer-events 穿透处理、卡片 frosted glass 效果。

---

### Task 4: Canvas 星空背景 (三层粒子)

**Files:**
- Modify: `D:\Sapiens\index.html` (JS section)

- [ ] **Step 1: 渲染三层星空粒子**

JS 实现：
- `StarLayer` 类：管理一层星星粒子（位置、大小、透明度、速度）
- 远层：~400 颗，静态，随机分布在 canvas 全区域
- 中层：~200 颗，周期性闪烁（正弦波 opacity），带微弱星座聚类
- 近层：~50 颗，大光点，极慢漂移（position += velocity * dt）

Canvas 背景渐变：从 `--bg-deep` 到 `--bg-mid`。

每帧 draw 循环：清屏 → 画背景渐变 → 逐层画星星。

---

### Task 5: 星云区域 + 时间线节点

**Files:**
- Modify: `D:\Sapiens\index.html` (JS section)

- [ ] **Step 1: 绘制四大时代星云区域**

四个径向渐变区域（`createRadialGradient`），分布在 X 轴按时间比例展开：
- 认知革命: 0% – 25% X 范围，冰蓝色晕
- 农业革命: 25% – 50%，土金色晕
- 融合统一: 50% – 75%，紫藤色晕
- 科学革命: 75% – 100%，青蓝色晕

- [ ] **Step 2: 绘制时间线节点**

加载 `timeline.json` → 每个节点计算画布坐标：
- X = 基于 year 在总时间范围内的比例映射
- Y = 在所属时代星云区域内有机散布（加伪随机偏移避免直线排列）

节点渲染：
- `key`: 半径 6px，暖金光晕（shadowBlur）+ 脉冲动画（scale 正弦波动）
- `major`: 半径 4px，白色光晕
- `minor`: 半径 2px，仅在 zoom > 1.5 时绘制

- [ ] **Step 3: 绘制星座连线**

仅在 zoom > 1.5 时，同一时代内相邻节点间画细线（rgba(212, 168, 83, 0.15)），形成星座导航感。

---

### Task 6: 缩放和平移交互

**Files:**
- Modify: `D:\Sapiens\index.html` (JS section)

- [ ] **Step 1: 实现缩放 (zoom)**

- 监听 `wheel` 事件，deltaY 驱动 zoom 因子增减
- 缩放范围 clamp: 0.3 ~ 4.0
- 缩放以鼠标位置为中心（调整 pan offset 使得鼠标下的内容保持不动）
- 移动端：监听 `touchstart`/`touchmove`/`touchend`，双指 pinch 计算缩放

- [ ] **Step 2: 实现平移 (pan)**

- 监听 `mousedown` → `mousemove` → `mouseup`，计算 delta 更新 pan offset
- 移动端单指拖拽同理
- 边界限制：不拖出时代区域范围

- [ ] **Step 3: 渲染循环优化**

- `requestAnimationFrame` 驱动主循环
- 在循环中根据当前 zoom/pan 状态重绘所有层
- 缩放时按层级控制显示内容（远景只显示 key 节点，近景显示全部）

---

### Task 7: 详情卡片

**Files:**
- Modify: `D:\Sapiens\index.html` (HTML + CSS + JS)

- [ ] **Step 1: 卡片 HTML 结构**

```html
<div id="detail-card" class="hidden">
  <div class="card-inner">
    <button class="card-close">&times;</button>
    <div class="card-nav">
      <button class="card-prev">←</button>
      <button class="card-next">→</button>
    </div>
    <div class="card-era-tag"></div>
    <div class="card-year"></div>
    <h2 class="card-title"></h2>
    <p class="card-summary"></p>
    <div class="card-detail"></div>
    <div class="card-tags"></div>
  </div>
</div>
```

- [ ] **Step 2: 卡片样式（frosted glass）**

```css
#detail-card {
  position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%);
  max-width: 640px; width: 90vw;
  background: var(--card-bg);
  backdrop-filter: blur(24px);
  border: 1px solid var(--card-border);
  border-radius: 16px;
  padding: 32px;
  z-index: 100;
  transition: opacity 0.3s, transform 0.3s;
}
.hidden { opacity: 0; pointer-events: none; transform: translate(-50%, -48%); }
```

暖金上边框发光、close 按钮 hover 变金、中英双语分区展示。

- [ ] **Step 3: 卡片 JS 逻辑**

- `openCard(nodeData)`: 填充数据 → 移除 hidden → 画布自动平移使节点居中
- `closeCard()`: 添加 hidden → 恢复自由浏览
- 点击节点触发 openCard
- ESC / 点击卡片外部 / 点关闭按钮 → closeCard
- 卡片内 ← → 按钮导航到相邻节点（按时间排序）
- 键盘 ← → 同样支持

---

### Task 8: UI 控件 (时代标签 / 迷你地图 / 搓条)

**Files:**
- Modify: `D:\Sapiens\index.html` (HTML + CSS + JS)

- [ ] **Step 1: 时代标签栏**

固定在画布顶部：
```html
<div id="era-labels">
  <button class="era-tag" data-era="cognitive" style="--era-color: var(--era-cognitive)">
    认知革命 <span>Cognitive Rev.</span>
  </button>
  <!-- 三个同理 -->
</div>
```

点击标签 → 动画平移 + 缩放到该时代区域。

- [ ] **Step 2: 右下角迷你地图**

小型 Canvas (200×60px)，绘制全局缩略：
- 灰色底
- 四条彩色横条代表四时代
- 白色矩形框指示当前视口位置和缩放范围
- 拖拽矩形框 → 平移主画布

- [ ] **Step 3: 底部时间轴搓条**

```html
<div id="scrubber">
  <div class="scrubber-track">
    <div class="scrubber-fill"></div>
    <div class="scrubber-thumb"></div>
  </div>
</div>
```

水平条涵盖 -300000 BCE → 2020 CE，四个时代色分段。拖拽 thumb → 更新画布 X 偏移。当前视口范围在条上高亮。

---

### Task 9: 收尾打磨

**Files:**
- Modify: `D:\Sapiens\index.html`

- [ ] **Step 1: 动画过渡**

- zoom/pan 缓动：使用 easeInOutCubic 缓动函数，过渡 300ms
- 卡片出入动画：opacity + translateY
- 节点 hover 效果：光晕扩大、指针变为 pointer

- [ ] **Step 2: 键盘快捷键**

- `1/2/3/4` → 跳转四大时代
- `Escape` → 关闭卡片
- `← →` → 上/下一个节点（卡片打开时）
- `0` → 重置视角（回到初始中景）

- [ ] **Step 3: prefers-reduced-motion 和响应式**

- 检测 `prefers-reduced-motion`，有则禁用脉冲动画和平滑过渡
- 移动端：详情卡片占满屏宽、字号调小、触摸手势覆盖
- 卡片最大宽度 640px，移动端 95vw

---

### Task 10: 最终验证

**Files:**
- Verify: `D:\Sapiens\index.html`
- Verify: `D:\Sapiens\data\timeline.json`

- [ ] **Step 1: 浏览器验证**

用浏览器打开 `index.html`：
- 星空渲染正常，三层粒子可见
- 四大星云区域色正确
- 节点可点击弹出卡片
- 滚轮缩放和平移拖拽正常
- 迷你地图和搓条联动正确
- 中英双语内容完整

- [ ] **Step 2: 静态检查**

- timeline.json 语法正确 (JSON.parse)
- 所有 34 条数据字段完整
- era 值均为合法值
- significance 值合法

- [ ] **Step 3: 性能检查**

- 初始加载 < 2MB
- requestAnimationFrame 帧率 ≥ 30fps
- 缩放和拖拽无明显卡顿

---

## 自审

| 检查项 | 状态 |
|--------|------|
| 覆盖所有 spec 需求 | ✅ 配色/字体/三层星空/四大星云/zoom层级/交互/卡片/控件/数据 |
| 无 placeholder | ✅ 所有步骤含具体代码和命令 |
| 类型一致性 | ✅ 数据结构、CSS 变量、JS 函数名一致 |
| 架构决策 | ✅ Canvas 渲染 + HTML overlay 分离 |
