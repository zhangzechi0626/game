# 网页版水排序游戏设计文档

## 项目概述

**项目名称**: 水排序游戏（Water Sort Puzzle）

**平台**: 网页（单HTML文件）

**技术栈**: 原生 HTML/CSS/JavaScript

**目标用户**: 所有浏览器用户，休闲益智游戏爱好者

**核心玩法**: 经典水排序游戏 - 通过倒水操作，让每个试管只包含一种颜色的水

**功能范围**: 基础版（5个关卡 + 手动选关 + 游戏玩法），不包含计时器、步数统计、撤销等进阶功能

**部署方式**: 单个HTML文件，双击在浏览器中打开即可运行，无需服务器

## 整体架构

### 文件结构

```
water-sort-game.html  （单文件包含所有内容）
```

一个HTML文件包含：
- HTML结构（页面布局）
- CSS样式（嵌入在`<style>`标签中）
- JavaScript代码（嵌入在`<script>`标签中）

### 架构设计

采用简单的三层架构：

1. **数据层**：JavaScript对象存储游戏状态
   - 关卡数据（预设的5个关卡配置）
   - 当前游戏状态（试管数组、选中状态等）

2. **逻辑层**：JavaScript函数处理游戏逻辑
   - 倒水规则判断
   - 胜利条件检查
   - 关卡初始化

3. **视图层**：DOM元素 + CSS样式
   - HTML定义页面结构
   - CSS定义视觉样式（渐变背景、圆角、阴影）
   - JavaScript动态更新DOM

### 核心设计原则

- **数据驱动**：游戏状态存储在JavaScript对象中，UI根据状态渲染
- **事件驱动**：用户点击触发事件，事件处理函数更新状态，状态变化触发UI更新
- **模块化函数**：将功能拆分为小函数（如`canPour()`、`checkWin()`等）
- **零依赖**：不使用任何第三方库或框架

## 数据结构设计

### 试管数据结构

试管用数组表示，从底部到顶部存储颜色：

```javascript
// 示例：一个试管有4层水，从下往上是红、蓝、红、蓝
tube = ['red', 'blue', 'red', 'blue']

// 空试管
emptyTube = []
```

### 关卡数据结构

```javascript
level = {
  level: 1,           // 关卡号
  tubes: [            // 所有试管的初始状态
    ['red', 'blue', 'red', 'blue'],
    ['green', 'red', 'blue', 'green'],
    ['blue', 'green', 'red', 'green'],
    [],  // 空试管
    []   // 空试管
  ],
  capacity: 4         // 每个试管容量
}
```

### 游戏状态

```javascript
gameState = {
  currentLevel: 1,        // 当前关卡
  tubes: [...],           // 当前试管状态（会随着游戏进行而变化）
  selectedTube: null,     // 选中的试管索引（null表示未选中）
  capacity: 4             // 试管容量
}
```

### 颜色映射

```javascript
colors = {
  'red': '#FF6B6B',
  'blue': '#4ECDC4',
  'green': '#95E1D3',
  'yellow': '#FFE66D',
  'purple': '#A8DADC',
  'orange': '#F4A261'
}
```

### 关卡配置

提供5个预设关卡，难度递增：

- **关卡1**: 3种颜色，5个试管（3个混合 + 2个空）
- **关卡2**: 3种颜色，5个试管，颜色分布更分散
- **关卡3**: 4种颜色，6个试管（4个混合 + 2个空）
- **关卡4**: 4种颜色，6个试管，混合更复杂
- **关卡5**: 5种颜色，7个试管（5个混合 + 2个空）

## 游戏逻辑设计

### 核心倒水规则

1. **可以倒水的条件：**
   - 源试管非空
   - 目标试管未满
   - 目标试管为空 OR 目标试管顶部颜色与源试管顶部颜色相同

2. **倒水行为：**
   - 一次倒水会倒出源试管顶部所有连续相同颜色的水
   - 倒入数量受目标试管剩余空间限制

3. **胜利条件：**
   - 所有试管满足以下条件之一：
     - 试管为空
     - 试管只包含一种颜色且装满

### 关键算法

#### 倒水算法

```javascript
function pourWater(fromIndex, toIndex) {
  // 1. 获取源试管和目标试管
  const fromTube = gameState.tubes[fromIndex]
  const toTube = gameState.tubes[toIndex]

  // 2. 检查是否可以倒水
  if (!canPour(fromTube, toTube)) {
    return false
  }

  // 3. 获取源试管顶部颜色
  const topColor = getTopColor(fromTube)

  // 4. 计算源试管顶部连续相同颜色的数量
  let pourCount = 0
  for (let i = fromTube.length - 1; i >= 0; i--) {
    if (fromTube[i] === topColor) {
      pourCount++
    } else {
      break
    }
  }

  // 5. 计算目标试管剩余空间
  const remainingSpace = gameState.capacity - toTube.length

  // 6. 实际倒水量取较小值
  const actualPourCount = Math.min(pourCount, remainingSpace)

  // 7. 执行倒水操作
  const water = fromTube.splice(fromTube.length - actualPourCount, actualPourCount)
  toTube.push(...water)

  return true
}
```

#### 胜利判断算法

```javascript
function checkWin() {
  for (let tube of gameState.tubes) {
    // 空试管，继续检查下一个
    if (tube.length === 0) {
      continue
    }

    // 未满，不符合条件
    if (tube.length !== gameState.capacity) {
      return false
    }

    // 检查是否只有一种颜色
    const firstColor = tube[0]
    for (let color of tube) {
      if (color !== firstColor) {
        return false
      }
    }
  }

  return true
}
```

#### 辅助函数

```javascript
// 获取试管顶部颜色
function getTopColor(tube) {
  if (tube.length === 0) return null
  return tube[tube.length - 1]
}

// 检查是否可以倒水
function canPour(fromTube, toTube) {
  // 源试管为空
  if (fromTube.length === 0) return false

  // 目标试管已满
  if (toTube.length >= gameState.capacity) return false

  // 目标试管为空，可以倒
  if (toTube.length === 0) return true

  // 顶部颜色相同，可以倒
  return getTopColor(fromTube) === getTopColor(toTube)
}
```

## UI设计

### 页面布局

```
+------------------------------------------+
|            水排序游戏                    |
|                                          |
|   [关卡1] [关卡2] [关卡3] [关卡4] [关卡5]  |
|                                          |
|     当前关卡: 1      [重新开始]          |
|                                          |
|    [试管] [试管] [试管] [试管] [试管]    |
|                                          |
|              (胜利提示弹窗)               |
+------------------------------------------+
```

### 视觉风格（简洁现代）

#### 背景
- 线性渐变：`linear-gradient(135deg, #667eea 0%, #764ba2 100%)`
- 紫色调，营造舒适的游戏氛围

#### 试管样式
- **容器**：白色边框（3px），底部圆角（10px）
- **背景**：半透明白色 `rgba(255, 255, 255, 0.1)`
- **尺寸**：宽度60px，高度240px（容量4时，每层60px）
- **选中状态**：
  - 边框颜色变为黄色 `#FFE66D`
  - 边框加粗到5px
  - 添加发光阴影 `box-shadow: 0 0 20px rgba(255, 230, 109, 0.6)`
  - 向上位移10px `transform: translateY(-10px)`

#### 水层样式
- 使用鲜艳的纯色（从颜色映射取值）
- 宽度100%，高度根据容量计算
- 平滑过渡动画 `transition: all 0.3s ease`

#### 按钮样式
- **关卡按钮**：
  - 白色背景，圆角12px
  - 数字大字体（36px），粗体
  - 阴影效果 `box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15)`
  - 点击时缩放 `transform: scale(0.95)`

- **重新开始按钮**：
  - 白色半透明背景 `rgba(255, 255, 255, 0.9)`
  - 圆角20px，内边距12px 24px
  - hover时透明度降低

#### 胜利弹窗
- 半透明黑色遮罩 `rgba(0, 0, 0, 0.7)`
- 白色内容区，大圆角（20px）
- 居中显示，阴影效果
- 按钮采用主题色和灰色搭配

### 响应式设计

使用CSS Flexbox实现自适应布局：

- **关卡选择器**：`display: flex; flex-wrap: wrap; gap: 15px`
  - 自动换行适应屏幕宽度

- **试管容器**：`display: flex; flex-wrap: wrap; justify-content: center; gap: 20px`
  - 居中对齐，自动调整间距
  - 适配桌面（1200px+）和移动设备（320px+）

- **最大宽度限制**：主容器 `max-width: 900px`，确保大屏幕下不过宽

## 交互流程

### 用户操作流程

```
1. 打开HTML文件
   ↓
2. 显示关卡选择区（5个关卡按钮）
   ↓
3. 点击关卡按钮
   ↓
4. 加载关卡数据，渲染试管
   ↓
5. 点击试管A
   ↓
6. 试管A高亮显示（选中状态）
   ↓
7. 点击试管B
   ↓
8a. 如果B是A自己 → 取消选中，回到步骤5
8b. 如果可以倒水 → 执行倒水，更新UI，检查胜利
8c. 如果不能倒水 → 显示提示，取消选中，回到步骤5
   ↓
9. 如果胜利 → 显示胜利弹窗
   ↓
10a. 点击"下一关" → 加载下一关卡，回到步骤4
10b. 点击"返回选关" → 回到步骤2
```

### 事件处理

- **关卡按钮点击**：`handleLevelSelect(level)`
  - 初始化关卡数据
  - 渲染试管
  - 显示游戏区

- **试管点击**：`handleTubeClick(index)`
  - 如果未选中：选中当前试管
  - 如果已选中：
    - 点击自己：取消选中
    - 点击其他：尝试倒水

- **重新开始按钮**：`handleRestart()`
  - 重置当前关卡状态
  - 重新渲染试管

- **下一关按钮**：`handleNextLevel()`
  - 加载下一关卡（如果存在）
  - 否则提示已完成所有关卡

### 提示反馈

- **无法倒水**：浏览器 `alert("无法倒水！")`
- **胜利**：显示模态弹窗，包含"恭喜过关"文字和按钮
- **按钮交互**：hover和active状态的视觉反馈

## 技术实现细节

### HTML结构

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>水排序游戏</title>
  <style>
    /* CSS样式 */
  </style>
</head>
<body>
  <div id="app">
    <h1>水排序游戏</h1>

    <!-- 关卡选择器 -->
    <div id="level-selector">
      <!-- 动态生成关卡按钮 -->
    </div>

    <!-- 游戏区 -->
    <div id="game-area" class="hidden">
      <div class="game-header">
        <span id="level-display">关卡 1</span>
        <button id="restart-btn">重新开始</button>
      </div>

      <!-- 试管容器 -->
      <div id="tubes-container">
        <!-- 动态生成试管 -->
      </div>
    </div>

    <!-- 胜利弹窗 -->
    <div id="win-modal" class="hidden">
      <div class="modal-overlay"></div>
      <div class="modal-content">
        <h2>🎉 恭喜过关！</h2>
        <div class="modal-buttons">
          <button id="next-btn">下一关</button>
          <button id="back-btn">返回选关</button>
        </div>
      </div>
    </div>
  </div>

  <script>
    /* JavaScript代码 */
  </script>
</body>
</html>
```

### CSS组织结构

1. **全局样式**
   - body样式、字体、边距
   - 容器居中和最大宽度

2. **关卡选择器样式**
   - 按钮布局和间距
   - 按钮样式和hover效果

3. **游戏区样式**
   - 游戏头部布局
   - 试管容器布局

4. **试管和水层样式**
   - 试管外观和尺寸
   - 选中状态样式
   - 水层样式和动画

5. **按钮样式**
   - 重新开始按钮
   - 胜利弹窗按钮

6. **弹窗样式**
   - 遮罩层
   - 内容区布局和样式

7. **工具类**
   - `.hidden { display: none; }`

### JavaScript代码组织

```javascript
// ============ 1. 数据定义 ============
const LEVELS = [
  // 关卡1-5的数据
]

const COLORS = {
  'red': '#FF6B6B',
  // ... 其他颜色
}

// ============ 2. 游戏状态 ============
let gameState = {
  currentLevel: null,
  tubes: [],
  selectedTube: null,
  capacity: 4
}

// ============ 3. 工具函数 ============
function getTopColor(tube) { ... }
function canPour(fromTube, toTube) { ... }
function deepCopy(obj) { return JSON.parse(JSON.stringify(obj)) }

// ============ 4. 游戏逻辑 ============
function pourWater(fromIndex, toIndex) { ... }
function checkWin() { ... }
function initLevel(level) { ... }

// ============ 5. UI渲染 ============
function renderLevelSelector() { ... }
function renderTubes() { ... }
function showWinModal() { ... }
function hideWinModal() { ... }
function showGameArea() { ... }
function hideGameArea() { ... }

// ============ 6. 事件处理 ============
function handleLevelSelect(level) { ... }
function handleTubeClick(index) { ... }
function handleRestart() { ... }
function handleNextLevel() { ... }
function handleBackToSelector() { ... }

// ============ 7. 初始化 ============
window.onload = function() {
  renderLevelSelector()
  setupEventListeners()
}
```

## 开发计划

### 实现步骤

1. **创建HTML基础结构**
   - 编写HTML骨架
   - 定义所有容器和元素

2. **编写CSS样式**
   - 全局样式和布局
   - 组件样式（按钮、试管、弹窗）
   - 动画和过渡效果

3. **实现数据层**
   - 定义关卡数据
   - 定义颜色映射
   - 初始化游戏状态

4. **实现游戏逻辑**
   - 工具函数（getTopColor、canPour等）
   - 倒水函数
   - 胜利判断函数
   - 关卡初始化函数

5. **实现UI渲染**
   - 关卡选择器渲染
   - 试管动态渲染
   - 弹窗显示/隐藏

6. **实现事件处理**
   - 关卡选择事件
   - 试管点击事件
   - 按钮点击事件

7. **测试和优化**
   - 功能测试（倒水规则、胜利判断）
   - 浏览器兼容性测试
   - UI细节优化

### 预期成果

完成后将得到：

✅ 一个单HTML文件，双击即可在浏览器中运行
✅ 5个可玩的关卡，难度递增
✅ 流畅的倒水交互和视觉反馈
✅ 简洁现代的UI设计
✅ 完整的游戏流程（选关、游戏、胜利、重开）
✅ 文件大小约10-15KB

## 后续扩展方向

如果需要增强功能，可以考虑：

1. **步数统计**：记录当前关卡使用的步数
2. **计时器**：记录完成关卡所用时间
3. **撤销功能**：允许撤销上一步操作
4. **提示功能**：AI提示下一步应该如何操作
5. **音效**：添加倒水音效和背景音乐
6. **更多关卡**：增加10-20个关卡
7. **难度选择**：简单、中等、困难模式
8. **本地存储**：保存游戏进度和最佳成绩
9. **分享功能**：生成分享链接
10. **自定义关卡**：允许玩家创建自己的关卡

---

**设计完成时间**: 2026-02-28
**设计者**: Claude Code
**版本**: v1.0
