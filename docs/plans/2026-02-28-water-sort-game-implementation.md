# 微信小程序水排序游戏实施计划

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** 构建一个原生微信小程序水排序游戏，包含关卡选择和游戏玩法

**Architecture:** 采用两页面架构（关卡选择页 + 游戏页），游戏逻辑与UI分离，使用View组件渲染试管，纯前端实现无需后端

**Tech Stack:** 微信小程序原生框架（WXML/WXSS/JavaScript）

---

## Task 1: 项目初始化与目录结构

**Files:**
- Create: `water-sort-game/project.config.json`
- Create: `water-sort-game/app.json`
- Create: `water-sort-game/app.js`
- Create: `water-sort-game/app.wxss`

**Step 1: 创建项目根目录**

```bash
mkdir water-sort-game
cd water-sort-game
```

**Step 2: 创建项目配置文件 project.config.json**

```json
{
  "description": "水排序游戏小程序项目配置",
  "appid": "touristappid",
  "projectname": "water-sort-game",
  "miniprogramRoot": "./",
  "setting": {
    "es6": true,
    "postcss": true,
    "minified": true,
    "urlCheck": false
  },
  "compileType": "miniprogram"
}
```

**Step 3: 创建全局配置文件 app.json**

```json
{
  "pages": [
    "pages/index/index",
    "pages/game/game"
  ],
  "window": {
    "navigationBarTitleText": "水排序游戏",
    "navigationBarBackgroundColor": "#4ECDC4",
    "navigationBarTextStyle": "white",
    "backgroundColor": "#f5f5f5",
    "backgroundTextStyle": "light"
  },
  "sitemapLocation": "sitemap.json"
}
```

**Step 4: 创建 app.js**

```javascript
App({
  onLaunch() {
    console.log('水排序游戏启动')
  },
  globalData: {
    userInfo: null
  }
})
```

**Step 5: 创建全局样式 app.wxss**

```css
page {
  background-color: #f5f5f5;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Helvetica', 'Arial', sans-serif;
}

.container {
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  box-sizing: border-box;
}
```

**Step 6: 创建 sitemap.json**

```json
{
  "desc": "关于本文件的更多信息，请参考文档 https://developers.weixin.qq.com/miniprogram/dev/framework/sitemap.html",
  "rules": [{
    "action": "allow",
    "page": "*"
  }]
}
```

**Step 7: 创建目录结构**

```bash
mkdir -p pages/index
mkdir -p pages/game
mkdir -p utils
```

---

## Task 2: 关卡数据配置

**Files:**
- Create: `water-sort-game/utils/level-data.js`

**Step 1: 创建关卡数据文件**

```javascript
// utils/level-data.js

/**
 * 关卡数据配置
 * 每个关卡包含：关卡号、试管数量、颜色数量、容量、初始状态
 */

const levels = [
  // 关卡 1 - 简单
  {
    level: 1,
    tubeCount: 5,
    colorCount: 3,
    capacity: 4,
    initialState: [
      ['red', 'blue', 'red', 'blue'],
      ['green', 'red', 'blue', 'green'],
      ['blue', 'green', 'red', 'green'],
      [],
      []
    ]
  },

  // 关卡 2 - 简单-中等
  {
    level: 2,
    tubeCount: 5,
    colorCount: 3,
    capacity: 4,
    initialState: [
      ['red', 'green', 'blue', 'red'],
      ['blue', 'red', 'green', 'blue'],
      ['green', 'blue', 'red', 'green'],
      [],
      []
    ]
  },

  // 关卡 3 - 中等
  {
    level: 3,
    tubeCount: 6,
    colorCount: 4,
    capacity: 4,
    initialState: [
      ['red', 'blue', 'yellow', 'red'],
      ['green', 'red', 'blue', 'green'],
      ['yellow', 'green', 'blue', 'yellow'],
      ['blue', 'yellow', 'red', 'green'],
      [],
      []
    ]
  },

  // 关卡 4 - 中等-较难
  {
    level: 4,
    tubeCount: 6,
    colorCount: 4,
    capacity: 4,
    initialState: [
      ['yellow', 'red', 'blue', 'green'],
      ['blue', 'yellow', 'green', 'red'],
      ['green', 'blue', 'red', 'yellow'],
      ['red', 'green', 'yellow', 'blue'],
      [],
      []
    ]
  },

  // 关卡 5 - 难
  {
    level: 5,
    tubeCount: 7,
    colorCount: 5,
    capacity: 4,
    initialState: [
      ['red', 'purple', 'blue', 'yellow'],
      ['green', 'red', 'purple', 'blue'],
      ['yellow', 'green', 'blue', 'red'],
      ['purple', 'yellow', 'green', 'purple'],
      ['blue', 'yellow', 'red', 'green'],
      [],
      []
    ]
  }
]

/**
 * 获取指定关卡数据
 * @param {number} level - 关卡号
 * @returns {object} 关卡数据对象
 */
function getLevelData(level) {
  const levelData = levels.find(item => item.level === level)
  if (!levelData) {
    console.error(`关卡 ${level} 不存在`)
    return levels[0] // 返回第一关作为默认
  }

  // 深拷贝初始状态，避免修改原数据
  return {
    ...levelData,
    initialState: JSON.parse(JSON.stringify(levelData.initialState))
  }
}

/**
 * 获取总关卡数
 * @returns {number} 总关卡数
 */
function getTotalLevels() {
  return levels.length
}

module.exports = {
  getLevelData,
  getTotalLevels
}
```

---

## Task 3: 游戏逻辑实现

**Files:**
- Create: `water-sort-game/utils/game-logic.js`

**Step 1: 创建游戏逻辑文件**

```javascript
// utils/game-logic.js

/**
 * 获取试管顶部颜色
 * @param {Array} tube - 试管数组
 * @returns {string|null} 顶部颜色，空试管返回null
 */
function getTopColor(tube) {
  if (tube.length === 0) {
    return null
  }
  return tube[tube.length - 1]
}

/**
 * 计算试管顶部连续相同颜色的数量
 * @param {Array} tube - 试管数组
 * @returns {number} 连续相同颜色的数量
 */
function countTopSameColor(tube) {
  if (tube.length === 0) {
    return 0
  }

  const topColor = tube[tube.length - 1]
  let count = 0

  for (let i = tube.length - 1; i >= 0; i--) {
    if (tube[i] === topColor) {
      count++
    } else {
      break
    }
  }

  return count
}

/**
 * 检查是否可以倒水
 * @param {Array} fromTube - 源试管
 * @param {Array} toTube - 目标试管
 * @param {number} capacity - 试管容量
 * @returns {boolean} 是否可以倒水
 */
function canPour(fromTube, toTube, capacity) {
  // 源试管为空，不能倒
  if (fromTube.length === 0) {
    return false
  }

  // 目标试管已满，不能倒
  if (toTube.length >= capacity) {
    return false
  }

  // 目标试管为空，可以倒
  if (toTube.length === 0) {
    return true
  }

  // 目标试管顶部颜色与源试管顶部颜色相同，可以倒
  const fromTopColor = getTopColor(fromTube)
  const toTopColor = getTopColor(toTube)

  return fromTopColor === toTopColor
}

/**
 * 执行倒水操作
 * @param {Array} tubes - 所有试管数组
 * @param {number} fromIndex - 源试管索引
 * @param {number} toIndex - 目标试管索引
 * @param {number} capacity - 试管容量
 * @returns {boolean} 是否倒水成功
 */
function pourWater(tubes, fromIndex, toIndex, capacity) {
  const fromTube = tubes[fromIndex]
  const toTube = tubes[toIndex]

  // 检查是否可以倒水
  if (!canPour(fromTube, toTube, capacity)) {
    return false
  }

  // 计算要倒出的水量
  const pourCount = countTopSameColor(fromTube)

  // 计算目标试管剩余空间
  const remainingSpace = capacity - toTube.length

  // 实际倒水量取较小值
  const actualPourCount = Math.min(pourCount, remainingSpace)

  // 执行倒水
  const water = fromTube.splice(fromTube.length - actualPourCount, actualPourCount)
  toTube.push(...water)

  return true
}

/**
 * 检查试管是否完成（空或单色且满）
 * @param {Array} tube - 试管数组
 * @param {number} capacity - 试管容量
 * @returns {boolean} 是否完成
 */
function isTubeComplete(tube, capacity) {
  // 空试管算完成
  if (tube.length === 0) {
    return true
  }

  // 未满不算完成
  if (tube.length !== capacity) {
    return false
  }

  // 检查是否只有一种颜色
  const firstColor = tube[0]
  for (let i = 1; i < tube.length; i++) {
    if (tube[i] !== firstColor) {
      return false
    }
  }

  return true
}

/**
 * 检查游戏是否胜利
 * @param {Array} tubes - 所有试管数组
 * @param {number} capacity - 试管容量
 * @returns {boolean} 是否胜利
 */
function checkWin(tubes, capacity) {
  for (let tube of tubes) {
    if (!isTubeComplete(tube, capacity)) {
      return false
    }
  }
  return true
}

module.exports = {
  getTopColor,
  countTopSameColor,
  canPour,
  pourWater,
  checkWin,
  isTubeComplete
}
```

---

## Task 4: 关卡选择页面

**Files:**
- Create: `water-sort-game/pages/index/index.js`
- Create: `water-sort-game/pages/index/index.wxml`
- Create: `water-sort-game/pages/index/index.wxss`
- Create: `water-sort-game/pages/index/index.json`

**Step 1: 创建 index.json**

```json
{
  "navigationBarTitleText": "选择关卡"
}
```

**Step 2: 创建 index.wxml**

```xml
<!--pages/index/index.wxml-->
<view class="container">
  <view class="header">
    <text class="title">水排序游戏</text>
    <text class="subtitle">Water Sort Puzzle</text>
  </view>

  <view class="level-list">
    <view
      class="level-item"
      wx:for="{{levels}}"
      wx:key="level"
      bindtap="selectLevel"
      data-level="{{item.level}}"
    >
      <text class="level-number">{{item.level}}</text>
      <text class="level-label">关卡</text>
    </view>
  </view>

  <view class="footer">
    <text class="tip">点击选择关卡开始游戏</text>
  </view>
</view>
```

**Step 3: 创建 index.wxss**

```css
/* pages/index/index.wxss */

.container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 40rpx;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.header {
  margin-top: 60rpx;
  margin-bottom: 80rpx;
  text-align: center;
}

.title {
  display: block;
  font-size: 56rpx;
  font-weight: bold;
  color: #ffffff;
  margin-bottom: 16rpx;
}

.subtitle {
  display: block;
  font-size: 28rpx;
  color: rgba(255, 255, 255, 0.8);
  letter-spacing: 2rpx;
}

.level-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 30rpx;
  max-width: 600rpx;
  margin-bottom: 60rpx;
}

.level-item {
  width: 160rpx;
  height: 160rpx;
  background-color: #ffffff;
  border-radius: 20rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  box-shadow: 0 8rpx 20rpx rgba(0, 0, 0, 0.15);
  transition: all 0.3s ease;
}

.level-item:active {
  transform: scale(0.95);
  box-shadow: 0 4rpx 10rpx rgba(0, 0, 0, 0.2);
}

.level-number {
  font-size: 60rpx;
  font-weight: bold;
  color: #667eea;
  line-height: 1;
  margin-bottom: 8rpx;
}

.level-label {
  font-size: 24rpx;
  color: #999;
}

.footer {
  margin-top: auto;
  padding-bottom: 40rpx;
}

.tip {
  font-size: 26rpx;
  color: rgba(255, 255, 255, 0.7);
}
```

**Step 4: 创建 index.js**

```javascript
// pages/index/index.js

const { getTotalLevels } = require('../../utils/level-data')

Page({
  data: {
    levels: []
  },

  onLoad() {
    this.initLevels()
  },

  /**
   * 初始化关卡列表
   */
  initLevels() {
    const totalLevels = getTotalLevels()
    const levels = []

    for (let i = 1; i <= totalLevels; i++) {
      levels.push({ level: i })
    }

    this.setData({ levels })
  },

  /**
   * 选择关卡
   */
  selectLevel(event) {
    const level = event.currentTarget.dataset.level

    wx.navigateTo({
      url: `/pages/game/game?level=${level}`
    })
  }
})
```

---

## Task 5: 游戏页面布局

**Files:**
- Create: `water-sort-game/pages/game/game.json`
- Create: `water-sort-game/pages/game/game.wxml`
- Create: `water-sort-game/pages/game/game.wxss`

**Step 1: 创建 game.json**

```json
{
  "navigationBarTitleText": "游戏"
}
```

**Step 2: 创建 game.wxml**

```xml
<!--pages/game/game.wxml-->
<view class="container">
  <!-- 顶部信息栏 -->
  <view class="header">
    <view class="level-info">
      <text class="level-text">关卡 {{currentLevel}}</text>
    </view>
    <view class="btn-restart" bindtap="restartGame">
      <text>重新开始</text>
    </view>
  </view>

  <!-- 试管容器 -->
  <view class="tubes-container">
    <view
      class="tube-wrapper"
      wx:for="{{tubes}}"
      wx:key="index"
      bindtap="selectTube"
      data-index="{{index}}"
    >
      <view class="tube {{selectedTube === index ? 'selected' : ''}}">
        <!-- 水层 -->
        <view
          class="water-layer"
          wx:for="{{item}}"
          wx:for-item="color"
          wx:key="*this"
          style="background-color: {{colorMap[color]}}; height: {{layerHeight}}rpx;"
        ></view>
      </view>
    </view>
  </view>

  <!-- 胜利提示 -->
  <view class="win-modal" wx:if="{{isWin}}">
    <view class="win-content">
      <text class="win-title">🎉 恭喜过关！</text>
      <view class="win-buttons">
        <view class="btn-next" bindtap="nextLevel" wx:if="{{hasNextLevel}}">
          <text>下一关</text>
        </view>
        <view class="btn-back" bindtap="backToIndex">
          <text>返回关卡列表</text>
        </view>
      </view>
    </view>
  </view>
</view>
```

**Step 3: 创建 game.wxss**

```css
/* pages/game/game.wxss */

.container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 20rpx;
}

/* 顶部信息栏 */
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20rpx 30rpx;
  margin-bottom: 40rpx;
}

.level-info {
  flex: 1;
}

.level-text {
  font-size: 36rpx;
  font-weight: bold;
  color: #ffffff;
}

.btn-restart {
  padding: 16rpx 32rpx;
  background-color: rgba(255, 255, 255, 0.9);
  border-radius: 40rpx;
  box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.1);
}

.btn-restart text {
  font-size: 28rpx;
  color: #667eea;
  font-weight: 500;
}

.btn-restart:active {
  opacity: 0.8;
}

/* 试管容器 */
.tubes-container {
  flex: 1;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-content: flex-start;
  gap: 30rpx;
  padding: 20rpx;
}

.tube-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.tube {
  width: 80rpx;
  height: 320rpx;
  border: 6rpx solid #ffffff;
  border-top: none;
  border-radius: 0 0 20rpx 20rpx;
  background-color: rgba(255, 255, 255, 0.1);
  display: flex;
  flex-direction: column-reverse;
  overflow: hidden;
  transition: all 0.2s ease;
  position: relative;
}

.tube.selected {
  border-color: #FFE66D;
  border-width: 8rpx;
  box-shadow: 0 0 20rpx rgba(255, 230, 109, 0.6);
  transform: translateY(-10rpx);
}

.water-layer {
  width: 100%;
  transition: all 0.3s ease;
}

/* 胜利提示 */
.win-modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.win-content {
  width: 560rpx;
  background-color: #ffffff;
  border-radius: 30rpx;
  padding: 60rpx 40rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  box-shadow: 0 20rpx 60rpx rgba(0, 0, 0, 0.3);
}

.win-title {
  font-size: 48rpx;
  font-weight: bold;
  color: #667eea;
  margin-bottom: 50rpx;
}

.win-buttons {
  display: flex;
  gap: 20rpx;
}

.btn-next,
.btn-back {
  padding: 24rpx 48rpx;
  border-radius: 40rpx;
  font-size: 30rpx;
  font-weight: 500;
}

.btn-next {
  background-color: #667eea;
  color: #ffffff;
}

.btn-back {
  background-color: #f5f5f5;
  color: #666;
}

.btn-next:active,
.btn-back:active {
  opacity: 0.8;
}
```

---

## Task 6: 游戏页面逻辑

**Files:**
- Create: `water-sort-game/pages/game/game.js`

**Step 1: 创建 game.js**

```javascript
// pages/game/game.js

const { getLevelData, getTotalLevels } = require('../../utils/level-data')
const { pourWater, checkWin } = require('../../utils/game-logic')

Page({
  data: {
    currentLevel: 1,
    tubes: [],
    capacity: 4,
    selectedTube: -1,
    isWin: false,
    hasNextLevel: false,
    layerHeight: 80,
    colorMap: {
      red: '#FF6B6B',
      blue: '#4ECDC4',
      green: '#95E1D3',
      yellow: '#FFE66D',
      purple: '#A8DADC',
      orange: '#F4A261'
    }
  },

  onLoad(options) {
    const level = parseInt(options.level) || 1
    this.setData({
      currentLevel: level
    })
    this.initGame()
  },

  /**
   * 初始化游戏
   */
  initGame() {
    const levelData = getLevelData(this.data.currentLevel)
    const totalLevels = getTotalLevels()

    // 计算每层水的高度
    const layerHeight = 320 / levelData.capacity

    this.setData({
      tubes: levelData.initialState,
      capacity: levelData.capacity,
      selectedTube: -1,
      isWin: false,
      hasNextLevel: this.data.currentLevel < totalLevels,
      layerHeight: layerHeight
    })
  },

  /**
   * 选择试管
   */
  selectTube(event) {
    const index = event.currentTarget.dataset.index
    const { selectedTube, tubes, capacity } = this.data

    // 如果还没有选中试管
    if (selectedTube === -1) {
      // 选中当前试管（即使是空的也可以选中）
      this.setData({
        selectedTube: index
      })
    } else {
      // 如果点击的是同一个试管，取消选中
      if (selectedTube === index) {
        this.setData({
          selectedTube: -1
        })
        return
      }

      // 尝试倒水
      const success = pourWater(tubes, selectedTube, index, capacity)

      if (success) {
        // 倒水成功，更新状态
        this.setData({
          tubes: tubes,
          selectedTube: -1
        })

        // 检查是否胜利
        const isWin = checkWin(tubes, capacity)
        if (isWin) {
          setTimeout(() => {
            this.setData({ isWin: true })
          }, 300)
        }
      } else {
        // 倒水失败，提示用户
        wx.showToast({
          title: '无法倒水',
          icon: 'none',
          duration: 1000
        })

        // 取消选中
        this.setData({
          selectedTube: -1
        })
      }
    }
  },

  /**
   * 重新开始游戏
   */
  restartGame() {
    wx.showModal({
      title: '确认重新开始',
      content: '当前进度将会丢失',
      success: (res) => {
        if (res.confirm) {
          this.initGame()
        }
      }
    })
  },

  /**
   * 下一关
   */
  nextLevel() {
    const nextLevel = this.data.currentLevel + 1
    const totalLevels = getTotalLevels()

    if (nextLevel <= totalLevels) {
      this.setData({
        currentLevel: nextLevel
      })
      this.initGame()
    } else {
      wx.showToast({
        title: '已完成所有关卡！',
        icon: 'success'
      })
    }
  },

  /**
   * 返回关卡列表
   */
  backToIndex() {
    wx.navigateBack()
  }
})
```

---

## Task 7: 测试与调试

**Step 1: 在微信开发者工具中打开项目**

1. 打开微信开发者工具
2. 选择"小程序" -> "导入项目"
3. 项目目录选择 `water-sort-game` 文件夹
4. AppID 选择 "测试号"
5. 点击"导入"

**Step 2: 测试关卡选择页**

预期行为：
- 显示 5 个关卡卡片
- 点击任意关卡能跳转到游戏页面
- 样式美观，渐变背景正常显示

**Step 3: 测试游戏基本功能**

测试用例：
1. 点击试管 -> 试管高亮显示
2. 点击另一个试管 -> 如果可以倒水，水层移动；如果不能倒水，显示提示
3. 再次点击同一试管 -> 取消选中
4. 点击空试管 -> 可以选中空试管

**Step 4: 测试倒水规则**

验证以下规则：
1. 只能倒到空试管或顶部颜色相同的试管
2. 一次倒水会倒出所有顶部连续相同颜色
3. 目标试管满了不能继续倒入

**Step 5: 测试胜利条件**

完成关卡 1：
1. 将所有颜色分类到不同试管
2. 确保每个试管要么为空，要么只有一种颜色且装满
3. 应该弹出胜利提示框

**Step 6: 测试重新开始功能**

1. 在游戏中点击"重新开始"按钮
2. 应该弹出确认对话框
3. 确认后试管恢复到初始状态

**Step 7: 测试下一关功能**

1. 完成关卡 1
2. 点击"下一关"按钮
3. 应该跳转到关卡 2
4. 在关卡 5 完成后点击下一关，应提示"已完成所有关卡"

**Step 8: 测试返回功能**

1. 在游戏页面点击"返回关卡列表"
2. 应该返回到关卡选择页

---

## Task 8: 优化与完善

**Files:**
- Modify: `water-sort-game/pages/game/game.wxss`

**Step 1: 添加触摸反馈优化**

在 game.wxss 中添加：

```css
/* 优化触摸反馈 */
.tube-wrapper:active .tube {
  opacity: 0.8;
}

.btn-restart:active {
  transform: scale(0.95);
}
```

**Step 2: 添加试管容器响应式布局**

修改 `.tubes-container`：

```css
.tubes-container {
  flex: 1;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-content: flex-start;
  gap: 30rpx;
  padding: 20rpx;
  max-width: 750rpx;
  margin: 0 auto;
}
```

**Step 3: 添加空试管提示**

在 game.wxml 的 tube 元素中添加：

```xml
<view class="tube {{selectedTube === index ? 'selected' : ''}}">
  <!-- 空试管提示 -->
  <view class="empty-hint" wx:if="{{item.length === 0}}">
    <text>空</text>
  </view>

  <!-- 水层 -->
  <view
    class="water-layer"
    wx:for="{{item}}"
    wx:for-item="color"
    wx:key="*this"
    style="background-color: {{colorMap[color]}}; height: {{layerHeight}}rpx;"
  ></view>
</view>
```

在 game.wxss 中添加：

```css
.empty-hint {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 24rpx;
  color: rgba(255, 255, 255, 0.5);
}
```

**Step 4: 添加移动次数统计（可选）**

如果需要显示移动次数，可以在 game.js 的 data 中添加：

```javascript
data: {
  // ...其他数据
  moveCount: 0
}
```

在 `selectTube` 方法中倒水成功后添加：

```javascript
if (success) {
  this.setData({
    tubes: tubes,
    selectedTube: -1,
    moveCount: this.data.moveCount + 1
  })
  // ...
}
```

在 game.wxml 的 header 中显示：

```xml
<view class="level-info">
  <text class="level-text">关卡 {{currentLevel}}</text>
  <text class="move-count">步数: {{moveCount}}</text>
</view>
```

在 game.wxss 中添加样式：

```css
.level-info {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 8rpx;
}

.move-count {
  font-size: 24rpx;
  color: rgba(255, 255, 255, 0.8);
}
```

---

## Task 9: 最终验收

**Step 1: 完整功能测试清单**

- [ ] 关卡选择页正常显示
- [ ] 可以选择任意关卡进入游戏
- [ ] 试管和水层正常显示
- [ ] 点击试管有高亮效果
- [ ] 倒水规则正确执行
- [ ] 无法倒水时显示提示
- [ ] 完成关卡后显示胜利提示
- [ ] 重新开始功能正常
- [ ] 下一关功能正常
- [ ] 返回关卡列表功能正常
- [ ] 所有 5 个关卡可以正常游玩

**Step 2: 性能检查**

在微信开发者工具中：
1. 打开"调试器" -> "Performance"
2. 玩一局游戏
3. 检查是否有性能问题
4. 确保页面渲染流畅

**Step 3: 样式检查**

1. 检查不同屏幕尺寸下的显示效果
2. 使用模拟器切换设备型号测试
3. 确保在小屏幕手机上也能正常显示

**Step 4: 真机预览（可选）**

1. 点击微信开发者工具右上角"预览"
2. 使用手机微信扫码
3. 在真机上测试游戏
4. 检查触摸反馈和性能

**Step 5: 代码审查**

检查代码质量：
- 变量命名清晰
- 函数职责单一
- 注释完整
- 无 console.error
- 无未使用的代码

---

## 完成标准

项目完成时应该具备：

✅ 5 个可玩的关卡
✅ 流畅的倒水交互
✅ 正确的游戏规则判断
✅ 美观的 UI 设计
✅ 完整的功能（选关、游戏、重开、下一关、返回）
✅ 良好的用户反馈（提示、动画、高亮）
✅ 代码结构清晰，易于维护

---

## 后续扩展建议

完成基础版后，可以考虑添加：

1. **撤销功能**：记录操作历史，允许撤销上一步
2. **提示功能**：AI 提示下一步应该怎么走
3. **计时器**：记录完成关卡所用时间
4. **最少步数**：显示关卡的最优解步数
5. **音效**：倒水声、完成关卡音效
6. **更多关卡**：添加更多难度的关卡
7. **关卡编辑器**：让用户自己设计关卡
8. **数据持久化**：保存游戏进度到本地存储
9. **分享功能**：分享游戏到好友或朋友圈
10. **排行榜**：需要后端支持，记录最快完成时间

---

**创建时间**: 2026-02-28
**预计完成时间**: 2-3 小时（新手可能需要 4-6 小时）
