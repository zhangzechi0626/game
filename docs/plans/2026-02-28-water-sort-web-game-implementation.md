# 网页版水排序游戏实施计划

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** 创建一个单HTML文件的网页版水排序游戏，双击即可在浏览器中运行

**Architecture:** 单文件包含HTML/CSS/JavaScript，采用数据驱动的事件响应式架构，使用原生DOM操作和Flexbox布局

**Tech Stack:** 原生 HTML5, CSS3, JavaScript (ES6+)

---

## Task 1: 创建HTML基础结构

**Files:**
- Create: `water-sort-game.html`

**Step 1: 创建HTML文档结构**

创建完整的HTML文件框架：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>水排序游戏</title>
  <style>
    /* CSS样式将在这里添加 */
  </style>
</head>
<body>
  <div id="app">
    <h1>水排序游戏</h1>

    <!-- 关卡选择器 -->
    <div id="level-selector"></div>

    <!-- 游戏区 -->
    <div id="game-area" class="hidden">
      <div class="game-header">
        <span id="level-display"></span>
        <button id="restart-btn">重新开始</button>
      </div>
      <div id="tubes-container"></div>
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
    // JavaScript代码将在这里添加
  </script>
</body>
</html>
```

**Step 2: 添加CSS样式**

在`<style>`标签中添加完整样式：

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Helvetica', 'Arial', sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

#app {
  width: 100%;
  max-width: 900px;
  text-align: center;
}

h1 {
  color: white;
  font-size: 48px;
  margin-bottom: 40px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
}

/* 关卡选择器样式 */
#level-selector {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 15px;
  margin-bottom: 30px;
}

.level-btn {
  width: 80px;
  height: 80px;
  background-color: white;
  border: none;
  border-radius: 12px;
  font-size: 36px;
  font-weight: bold;
  color: #667eea;
  cursor: pointer;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);
  transition: all 0.2s ease;
}

.level-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.2);
}

.level-btn:active {
  transform: scale(0.95);
}

/* 游戏区样式 */
#game-area {
  background-color: rgba(255, 255, 255, 0.1);
  border-radius: 20px;
  padding: 30px;
  backdrop-filter: blur(10px);
}

.game-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
}

#level-display {
  font-size: 24px;
  font-weight: bold;
  color: white;
}

#restart-btn {
  padding: 12px 24px;
  background-color: rgba(255, 255, 255, 0.9);
  border: none;
  border-radius: 20px;
  font-size: 16px;
  color: #667eea;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
}

#restart-btn:hover {
  background-color: white;
  transform: translateY(-2px);
}

#restart-btn:active {
  transform: scale(0.95);
}

/* 试管容器样式 */
#tubes-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 20px;
  padding: 20px;
}

.tube {
  width: 60px;
  height: 240px;
  border: 3px solid white;
  border-top: none;
  border-radius: 0 0 10px 10px;
  background-color: rgba(255, 255, 255, 0.1);
  display: flex;
  flex-direction: column-reverse;
  overflow: hidden;
  cursor: pointer;
  transition: all 0.2s ease;
  position: relative;
}

.tube:hover {
  transform: translateY(-5px);
}

.tube.selected {
  border-color: #FFE66D;
  border-width: 5px;
  box-shadow: 0 0 20px rgba(255, 230, 109, 0.6);
  transform: translateY(-10px);
}

.water-layer {
  width: 100%;
  transition: all 0.3s ease;
}

.empty-hint {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 12px;
  color: rgba(255, 255, 255, 0.5);
  pointer-events: none;
}

/* 胜利弹窗样式 */
#win-modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.7);
}

.modal-content {
  position: relative;
  background-color: white;
  border-radius: 20px;
  padding: 40px;
  text-align: center;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  z-index: 1;
}

.modal-content h2 {
  font-size: 36px;
  color: #667eea;
  margin-bottom: 30px;
}

.modal-buttons {
  display: flex;
  gap: 15px;
  justify-content: center;
}

.modal-buttons button {
  padding: 15px 30px;
  border: none;
  border-radius: 25px;
  font-size: 18px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
}

#next-btn {
  background-color: #667eea;
  color: white;
}

#back-btn {
  background-color: #f5f5f5;
  color: #666;
}

.modal-buttons button:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
}

.modal-buttons button:active {
  transform: scale(0.95);
}

/* 工具类 */
.hidden {
  display: none !important;
}

/* 响应式设计 */
@media (max-width: 600px) {
  h1 {
    font-size: 32px;
    margin-bottom: 20px;
  }

  .level-btn {
    width: 60px;
    height: 60px;
    font-size: 28px;
  }

  #game-area {
    padding: 20px;
  }

  .tube {
    width: 50px;
    height: 200px;
  }

  #tubes-container {
    gap: 15px;
  }
}
```

**Step 3: 在浏览器中验证**

在浏览器中打开HTML文件，验证：
- 页面显示"水排序游戏"标题
- 背景渐变正常显示
- 布局居中显示

---

## Task 2: 实现数据层

**Files:**
- Modify: `water-sort-game.html` (在`<script>`标签中)

**Step 1: 定义关卡数据**

在`<script>`标签中添加关卡数据：

```javascript
// ============ 数据定义 ============
const LEVELS = [
  {
    level: 1,
    tubes: [
      ['red', 'blue', 'red', 'blue'],
      ['green', 'red', 'blue', 'green'],
      ['blue', 'green', 'red', 'green'],
      [],
      []
    ],
    capacity: 4
  },
  {
    level: 2,
    tubes: [
      ['red', 'green', 'blue', 'red'],
      ['blue', 'red', 'green', 'blue'],
      ['green', 'blue', 'red', 'green'],
      [],
      []
    ],
    capacity: 4
  },
  {
    level: 3,
    tubes: [
      ['red', 'blue', 'yellow', 'red'],
      ['green', 'red', 'blue', 'green'],
      ['yellow', 'green', 'blue', 'yellow'],
      ['blue', 'yellow', 'red', 'green'],
      [],
      []
    ],
    capacity: 4
  },
  {
    level: 4,
    tubes: [
      ['yellow', 'red', 'blue', 'green'],
      ['blue', 'yellow', 'green', 'red'],
      ['green', 'blue', 'red', 'yellow'],
      ['red', 'green', 'yellow', 'blue'],
      [],
      []
    ],
    capacity: 4
  },
  {
    level: 5,
    tubes: [
      ['red', 'purple', 'blue', 'yellow'],
      ['green', 'red', 'purple', 'blue'],
      ['yellow', 'green', 'blue', 'red'],
      ['purple', 'yellow', 'green', 'purple'],
      ['blue', 'yellow', 'red', 'green'],
      [],
      []
    ],
    capacity: 4
  }
];

const COLORS = {
  'red': '#FF6B6B',
  'blue': '#4ECDC4',
  'green': '#95E1D3',
  'yellow': '#FFE66D',
  'purple': '#A8DADC',
  'orange': '#F4A261'
};
```

**Step 2: 初始化游戏状态**

添加游戏状态变量：

```javascript
// ============ 游戏状态 ============
let gameState = {
  currentLevel: null,
  tubes: [],
  selectedTube: null,
  capacity: 4
};
```

**Step 3: 验证数据**

在浏览器控制台中验证：
```javascript
console.log('关卡数量:', LEVELS.length);
console.log('颜色数量:', Object.keys(COLORS).length);
console.log('游戏状态:', gameState);
```

---

## Task 3: 实现游戏逻辑函数

**Files:**
- Modify: `water-sort-game.html` (在`<script>`标签中)

**Step 1: 实现工具函数**

```javascript
// ============ 工具函数 ============
function getTopColor(tube) {
  if (tube.length === 0) return null;
  return tube[tube.length - 1];
}

function canPour(fromTube, toTube) {
  // 源试管为空
  if (fromTube.length === 0) return false;

  // 目标试管已满
  if (toTube.length >= gameState.capacity) return false;

  // 目标试管为空，可以倒
  if (toTube.length === 0) return true;

  // 顶部颜色相同，可以倒
  return getTopColor(fromTube) === getTopColor(toTube);
}

function deepCopy(obj) {
  return JSON.parse(JSON.stringify(obj));
}
```

**Step 2: 实现倒水函数**

```javascript
// ============ 游戏逻辑 ============
function pourWater(fromIndex, toIndex) {
  const fromTube = gameState.tubes[fromIndex];
  const toTube = gameState.tubes[toIndex];

  // 检查是否可以倒水
  if (!canPour(fromTube, toTube)) {
    return false;
  }

  // 获取源试管顶部颜色
  const topColor = getTopColor(fromTube);

  // 计算源试管顶部连续相同颜色的数量
  let pourCount = 0;
  for (let i = fromTube.length - 1; i >= 0; i--) {
    if (fromTube[i] === topColor) {
      pourCount++;
    } else {
      break;
    }
  }

  // 计算目标试管剩余空间
  const remainingSpace = gameState.capacity - toTube.length;

  // 实际倒水量取较小值
  const actualPourCount = Math.min(pourCount, remainingSpace);

  // 执行倒水操作
  const water = fromTube.splice(fromTube.length - actualPourCount, actualPourCount);
  toTube.push(...water);

  return true;
}
```

**Step 3: 实现胜利判断函数**

```javascript
function checkWin() {
  for (let tube of gameState.tubes) {
    // 空试管，继续
    if (tube.length === 0) {
      continue;
    }

    // 未满，不符合
    if (tube.length !== gameState.capacity) {
      return false;
    }

    // 检查是否只有一种颜色
    const firstColor = tube[0];
    for (let color of tube) {
      if (color !== firstColor) {
        return false;
      }
    }
  }

  return true;
}
```

**Step 4: 实现关卡初始化函数**

```javascript
function initLevel(level) {
  const levelData = LEVELS.find(l => l.level === level);
  if (!levelData) {
    console.error('关卡不存在:', level);
    return;
  }

  gameState.currentLevel = level;
  gameState.tubes = deepCopy(levelData.tubes);
  gameState.capacity = levelData.capacity;
  gameState.selectedTube = null;
}
```

**Step 5: 在控制台测试逻辑**

```javascript
// 测试代码（可以在控制台运行）
initLevel(1);
console.log('初始状态:', gameState.tubes);
pourWater(0, 3);
console.log('倒水后:', gameState.tubes);
console.log('是否胜利:', checkWin());
```

---

## Task 4: 实现UI渲染函数

**Files:**
- Modify: `water-sort-game.html` (在`<script>`标签中)

**Step 1: 实现关卡选择器渲染**

```javascript
// ============ UI渲染 ============
function renderLevelSelector() {
  const selector = document.getElementById('level-selector');
  selector.innerHTML = '';

  LEVELS.forEach(level => {
    const btn = document.createElement('button');
    btn.className = 'level-btn';
    btn.textContent = level.level;
    btn.onclick = () => handleLevelSelect(level.level);
    selector.appendChild(btn);
  });
}
```

**Step 2: 实现试管渲染**

```javascript
function renderTubes() {
  const container = document.getElementById('tubes-container');
  container.innerHTML = '';

  gameState.tubes.forEach((tube, index) => {
    const tubeDiv = document.createElement('div');
    tubeDiv.className = 'tube';
    if (gameState.selectedTube === index) {
      tubeDiv.classList.add('selected');
    }
    tubeDiv.onclick = () => handleTubeClick(index);

    // 如果试管为空，显示提示
    if (tube.length === 0) {
      const hint = document.createElement('div');
      hint.className = 'empty-hint';
      hint.textContent = '空';
      tubeDiv.appendChild(hint);
    }

    // 渲染水层
    const layerHeight = 240 / gameState.capacity;
    tube.forEach(color => {
      const layer = document.createElement('div');
      layer.className = 'water-layer';
      layer.style.height = layerHeight + 'px';
      layer.style.backgroundColor = COLORS[color];
      tubeDiv.appendChild(layer);
    });

    container.appendChild(tubeDiv);
  });
}
```

**Step 3: 实现弹窗显示/隐藏**

```javascript
function showWinModal() {
  document.getElementById('win-modal').classList.remove('hidden');
}

function hideWinModal() {
  document.getElementById('win-modal').classList.add('hidden');
}

function showGameArea() {
  document.getElementById('game-area').classList.remove('hidden');
}

function hideGameArea() {
  document.getElementById('game-area').classList.add('hidden');
}

function updateLevelDisplay() {
  document.getElementById('level-display').textContent = '关卡 ' + gameState.currentLevel;
}
```

**Step 4: 验证渲染**

在浏览器中测试：
```javascript
renderLevelSelector();
initLevel(1);
showGameArea();
updateLevelDisplay();
renderTubes();
```

应该看到：
- 5个关卡按钮
- 显示"关卡 1"
- 5个试管，其中3个有水，2个空

---

## Task 5: 实现事件处理函数

**Files:**
- Modify: `water-sort-game.html` (在`<script>`标签中)

**Step 1: 实现关卡选择事件**

```javascript
// ============ 事件处理 ============
function handleLevelSelect(level) {
  initLevel(level);
  showGameArea();
  updateLevelDisplay();
  renderTubes();
  hideWinModal();
}
```

**Step 2: 实现试管点击事件**

```javascript
function handleTubeClick(index) {
  // 如果未选中试管
  if (gameState.selectedTube === null) {
    gameState.selectedTube = index;
    renderTubes();
    return;
  }

  // 如果点击的是同一个试管，取消选中
  if (gameState.selectedTube === index) {
    gameState.selectedTube = null;
    renderTubes();
    return;
  }

  // 尝试倒水
  const success = pourWater(gameState.selectedTube, index);

  if (success) {
    // 倒水成功
    gameState.selectedTube = null;
    renderTubes();

    // 检查是否胜利
    if (checkWin()) {
      setTimeout(() => {
        showWinModal();
      }, 300);
    }
  } else {
    // 倒水失败
    alert('无法倒水！');
    gameState.selectedTube = null;
    renderTubes();
  }
}
```

**Step 3: 实现重新开始事件**

```javascript
function handleRestart() {
  const confirmed = confirm('确认重新开始当前关卡？');
  if (confirmed) {
    initLevel(gameState.currentLevel);
    renderTubes();
    hideWinModal();
  }
}
```

**Step 4: 实现下一关事件**

```javascript
function handleNextLevel() {
  const nextLevel = gameState.currentLevel + 1;

  if (nextLevel <= LEVELS.length) {
    handleLevelSelect(nextLevel);
  } else {
    alert('恭喜！你已经完成所有关卡！');
  }
}
```

**Step 5: 实现返回选关事件**

```javascript
function handleBackToSelector() {
  hideGameArea();
  hideWinModal();
  gameState.currentLevel = null;
  gameState.tubes = [];
  gameState.selectedTube = null;
}
```

**Step 6: 绑定事件监听器**

```javascript
function setupEventListeners() {
  document.getElementById('restart-btn').onclick = handleRestart;
  document.getElementById('next-btn').onclick = handleNextLevel;
  document.getElementById('back-btn').onclick = handleBackToSelector;
}
```

---

## Task 6: 初始化和测试

**Files:**
- Modify: `water-sort-game.html` (在`<script>`标签中)

**Step 1: 添加初始化代码**

在所有函数定义之后添加：

```javascript
// ============ 初始化 ============
window.onload = function() {
  console.log('游戏加载完成');
  renderLevelSelector();
  setupEventListeners();
  hideGameArea();
  hideWinModal();
};
```

**Step 2: 完整功能测试**

在浏览器中打开HTML文件，测试以下功能：

测试清单：
- [ ] 页面加载显示5个关卡按钮
- [ ] 点击关卡按钮进入游戏
- [ ] 显示正确的关卡号和试管
- [ ] 点击试管有高亮效果
- [ ] 点击第二个试管执行倒水
- [ ] 无法倒水时显示提示
- [ ] 点击同一个试管取消选中
- [ ] 完成关卡显示胜利弹窗
- [ ] 点击"下一关"加载下一关卡
- [ ] 点击"返回选关"回到关卡选择
- [ ] 点击"重新开始"重置关卡

**Step 3: 验证所有关卡**

逐个测试5个关卡：
- 关卡1：3种颜色，容易
- 关卡2：3种颜色，稍难
- 关卡3：4种颜色，中等
- 关卡4：4种颜色，较难
- 关卡5：5种颜色，困难

**Step 4: 浏览器兼容性测试**

在不同浏览器中测试：
- Chrome
- Firefox
- Safari（如果可用）
- Edge

**Step 5: 响应式测试**

测试不同屏幕尺寸：
- 桌面（>900px）
- 平板（600-900px）
- 手机（<600px）

使用浏览器开发者工具的设备模拟器测试。

---

## Task 7: 优化和完善

**Files:**
- Modify: `water-sort-game.html`

**Step 1: 添加页面标题图标（可选）**

在`<head>`中添加：

```html
<link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>🧪</text></svg>">
```

**Step 2: 优化CSS动画**

确保所有过渡动画流畅：
- 试管选中动画
- 按钮hover效果
- 弹窗显示动画

**Step 3: 添加代码注释**

为关键函数添加注释：
```javascript
/**
 * 倒水函数
 * @param {number} fromIndex - 源试管索引
 * @param {number} toIndex - 目标试管索引
 * @returns {boolean} 是否倒水成功
 */
function pourWater(fromIndex, toIndex) {
  // ...
}
```

**Step 4: 代码格式化**

确保代码格式一致：
- 缩进：2空格
- 分号一致使用
- 命名规范统一

**Step 5: 最终检查**

- [ ] HTML结构正确
- [ ] CSS样式美观
- [ ] JavaScript逻辑正确
- [ ] 无控制台错误
- [ ] 所有功能正常
- [ ] 响应式布局正常

---

## 完成标准

项目完成时应该具备：

✅ 单个HTML文件，双击在浏览器中运行
✅ 5个可玩的关卡，难度递增
✅ 流畅的倒水交互和视觉反馈
✅ 简洁现代的UI设计（渐变背景、圆角、阴影）
✅ 完整的游戏流程（选关、游戏、胜利、重开、返回）
✅ 响应式设计，适配桌面和移动设备
✅ 代码清晰，易于维护
✅ 文件大小约10-15KB

---

## 后续扩展建议（可选）

如果需要增强功能，可以考虑：

1. **步数统计**：在游戏状态中添加 moveCount，每次倒水后增加
2. **本地存储**：使用 localStorage 保存游戏进度
3. **更多关卡**：在 LEVELS 数组中添加更多关卡配置
4. **音效**：使用 Web Audio API 添加倒水音效
5. **动画优化**：使用 CSS transitions 实现更流畅的倒水动画
6. **撤销功能**：记录操作历史，允许撤销上一步

---

**创建时间**: 2026-02-28
**预计完成时间**: 30-60分钟
