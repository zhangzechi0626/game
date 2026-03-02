# 微信小程序水排序游戏设计文档

## 项目概述

**项目名称**: 水排序游戏（Water Sort Game）

**平台**: 微信小程序

**技术栈**: 微信小程序原生框架（WXML/WXSS/JavaScript）

**目标用户**: 微信用户，休闲益智游戏爱好者

**核心玩法**: 经典水排序游戏 - 通过倒水操作，让每个试管只包含一种颜色的水

**功能范围**: 基础版（关卡游戏 + 手动选关），不包含解锁系统、计分、提示等进阶功能

## 项目架构

### 文件结构

```
water-sort-game/
├── pages/
│   ├── index/              # 主页（关卡选择）
│   │   ├── index.js
│   │   ├── index.wxml
│   │   ├── index.wxss
│   │   └── index.json
│   └── game/               # 游戏页面
│       ├── game.js
│       ├── game.wxml
│       ├── game.wxss
│       └── game.json
├── utils/
│   ├── game-logic.js       # 游戏逻辑（倒水规则、胜利判断）
│   └── level-data.js       # 关卡数据配置
├── app.js
├── app.json
├── app.wxss
└── project.config.json
```

### 页面架构

采用简单的两页面架构：

1. **主页 (index)**
   - 显示关卡列表
   - 用户点击选择关卡
   - 跳转到游戏页面并传递关卡参数

2. **游戏页 (game)**
   - 显示试管阵列
   - 处理倒水交互
   - 判断胜利条件
   - 提供重新开始功能

### 核心设计原则

- **数据与视图分离**: 游戏逻辑封装在 `utils/game-logic.js`
- **关卡数据外置**: 便于后续添加新关卡，存储在 `utils/level-data.js`
- **简单状态管理**: 使用页面级 data，不引入额外状态管理库

## 数据结构设计

### 试管数据结构

```javascript
// 每个试管用数组表示，从底部到顶部存储颜色
// 例如：['red', 'blue', 'red', 'blue']
// 表示：底部是红色，从下往上依次是 红->蓝->红->蓝

tubes: [
  ['red', 'blue', 'red', 'blue'],     // 试管1
  ['green', 'red', 'blue', 'green'],  // 试管2
  ['blue', 'green', 'red', 'green'],  // 试管3
  [],                                  // 空试管4
  []                                   // 空试管5
]
```

### 关卡数据结构

```javascript
{
  level: 1,                // 关卡号
  tubeCount: 5,           // 试管总数
  colorCount: 3,          // 颜色种类数
  capacity: 4,            // 每个试管容量（层数）
  initialState: [         // 初始试管状态
    ['red', 'blue', 'red', 'blue'],
    ['green', 'red', 'blue', 'green'],
    ['blue', 'green', 'red', 'green'],
    [],
    []
  ]
}
```

### 游戏状态

```javascript
{
  currentLevel: 1,        // 当前关卡号
  tubes: [...],           // 当前试管状态
  selectedTube: -1,       // 选中的试管索引（-1表示未选中）
  isWin: false,          // 是否胜利
  moveCount: 0           // 移动次数（可选）
}
```

## 核心游戏逻辑

### 倒水规则

1. **源试管要求**: 必须非空
2. **目标试管要求**: 必须未满
3. **颜色匹配规则**:
   - 可以倒入空试管
   - 可以倒入顶部颜色相同的试管
4. **倒水数量**: 一次倒水会倒出源试管顶部所有连续相同颜色的水

### 胜利条件

所有试管满足以下条件之一：
- 试管为空
- 试管只包含一种颜色且装满（达到容量上限）

### 倒水算法

```javascript
function pourWater(tubes, fromIndex, toIndex) {
  // 1. 获取源试管顶部颜色
  const fromTube = tubes[fromIndex]
  const topColor = fromTube[fromTube.length - 1]

  // 2. 计算源试管顶部连续相同颜色的数量
  let pourCount = 0
  for (let i = fromTube.length - 1; i >= 0; i--) {
    if (fromTube[i] === topColor) {
      pourCount++
    } else {
      break
    }
  }

  // 3. 检查目标试管是否可接收
  const toTube = tubes[toIndex]
  const capacity = 4  // 从关卡数据获取

  if (toTube.length === 0) {
    // 目标试管为空，可以倒入
  } else if (toTube[toTube.length - 1] === topColor) {
    // 目标试管顶部颜色相同，可以倒入
  } else {
    return false  // 不能倒入
  }

  // 4. 计算实际可倒入数量（考虑目标试管剩余空间）
  const remainingSpace = capacity - toTube.length
  const actualPourCount = Math.min(pourCount, remainingSpace)

  // 5. 执行倒水操作
  const water = fromTube.splice(fromTube.length - actualPourCount, actualPourCount)
  toTube.push(...water)

  return true
}
```

### 胜利判断算法

```javascript
function checkWin(tubes, capacity) {
  for (let tube of tubes) {
    if (tube.length === 0) {
      // 空试管，符合条件
      continue
    }

    if (tube.length !== capacity) {
      // 未装满，不符合条件
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

## UI设计

### 主页（关卡选择页）

**布局**:
- 顶部：游戏标题 "水排序游戏"
- 中间：关卡列表，采用网格布局（3列）
- 每个关卡显示为卡片，包含关卡号

**样式**:
- 背景：浅色渐变（#f5f5f5 到 #e0e0e0）
- 卡片：白色背景，圆角边框，阴影效果
- 关卡号：大号字体，居中显示

**交互**:
- 点击关卡卡片 -> 跳转到游戏页面
- 使用 `wx.navigateTo` 传递关卡参数

### 游戏页

**布局**:
```
+----------------------------------+
|  关卡 1          [重新开始]      |
+----------------------------------+
|                                  |
|        [试管] [试管] [试管]      |
|        [试管] [试管]             |
|                                  |
+----------------------------------+
```

**试管样式**:
- 使用 View 组件实现
- 竖向长方形，固定宽度，高度根据容量计算
- 边框：2px 实线，深灰色
- 底部圆角：模拟试管底部
- 水层：每层用不同颜色的 View 块表示
- 试管间距：20rpx

**颜色方案**:
```javascript
colors: {
  red: '#FF6B6B',
  blue: '#4ECDC4',
  green: '#95E1D3',
  yellow: '#FFE66D',
  purple: '#A8DADC',
  orange: '#F4A261'
}
```

**交互状态**:
- **未选中**: 正常样式
- **选中**: 边框加粗，添加阴影，背景色高亮
- **不可操作**: 透明度降低

**动画效果**:
- 倒水动画：水块从源试管上升 -> 移动 -> 下降到目标试管
- 胜利动画：试管轻微跳动
- 使用微信小程序动画 API (`wx.createAnimation`)

### 胜利提示

**样式**:
- 模态框弹出，半透明遮罩
- 显示 "恭喜过关！" 文字
- 提供两个按钮：
  - "下一关"（如果有）
  - "返回关卡列表"

## 关卡设计

提供 5 个预设关卡，难度递增：

### 关卡 1（简单）
- 3 种颜色
- 5 个试管（3个混合 + 2个空）
- 容量：4层

### 关卡 2（简单-中等）
- 3 种颜色
- 5 个试管（3个混合 + 2个空）
- 容量：4层
- 颜色分布更分散

### 关卡 3（中等）
- 4 种颜色
- 6 个试管（4个混合 + 2个空）
- 容量：4层

### 关卡 4（中等-较难）
- 4 种颜色
- 6 个试管（4个混合 + 2个空）
- 容量：4层
- 颜色混合更复杂

### 关卡 5（难）
- 5 种颜色
- 7 个试管（5个混合 + 2个空）
- 容量：4层

## 技术实现细节

### 渲染方案选择

**选择 View 组件而非 Canvas**:
- 对新手更友好，直接用 WXML 布局
- 不需要手动绘制图形
- 使用 Flex 布局实现响应式
- 事件处理更简单

### 交互流程

```
状态：未选中
    ↓
用户点击试管A
    ↓
状态：选中试管A（记录 selectedTube = A，添加高亮）
    ↓
用户点击试管B
    ↓
判断能否倒水
    ├─ 可以倒 → 执行倒水动画 → 更新数据 → 检查胜利 → 重置选中状态
    └─ 不能倒 → 提示用户 → 重置选中状态

特殊情况：
- 用户再次点击试管A → 取消选中
- 用户点击空试管 → 如果未选中，则选中；如果已选中其他，则尝试倒水
```

### 数据流

```
utils/level-data.js (关卡配置)
        ↓
pages/game/game.js onLoad (加载关卡数据)
        ↓
this.setData({tubes: ...}) (初始化游戏状态)
        ↓
用户交互 (点击试管)
        ↓
utils/game-logic.js (执行倒水逻辑)
        ↓
this.setData({tubes: ...}) (更新游戏状态)
        ↓
检查胜利条件
        ↓
弹出胜利提示
```

### 动画实现

使用微信小程序动画 API：

```javascript
// 创建动画实例
const animation = wx.createAnimation({
  duration: 300,
  timingFunction: 'ease'
})

// 倒水动画序列
animation.translateY(-50).step()  // 上升
animation.translateX(100).step()  // 横移
animation.translateY(50).step()   // 下降

// 应用动画
this.setData({
  animationData: animation.export()
})
```

### 页面传参

主页跳转到游戏页时传递关卡号：

```javascript
// index.js
wx.navigateTo({
  url: '/pages/game/game?level=1'
})

// game.js
onLoad(options) {
  const level = parseInt(options.level)
  // 加载关卡数据
}
```

## 开发环境

### 工具准备

1. **微信开发者工具**
   - 下载地址：https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html
   - 选择稳定版（Stable Build）

2. **微信小程序账号**
   - 可使用测试号（无需注册）
   - 或注册正式账号：https://mp.weixin.qq.com/

### 项目配置

**project.config.json**:
```json
{
  "appid": "测试号AppID",
  "projectname": "water-sort-game",
  "miniprogramRoot": "./",
  "setting": {
    "es6": true,
    "postcss": true,
    "minified": true
  }
}
```

**app.json**:
```json
{
  "pages": [
    "pages/index/index",
    "pages/game/game"
  ],
  "window": {
    "navigationBarTitleText": "水排序游戏",
    "navigationBarBackgroundColor": "#4ECDC4",
    "backgroundColor": "#f5f5f5"
  }
}
```

### 调试方案

- 使用微信开发者工具的控制台查看日志
- 使用 `console.log` 输出关键状态
- 使用模拟器预览效果
- 真机调试测试性能

## 实现步骤

### 阶段一：项目初始化
1. 创建微信小程序项目
2. 配置项目结构
3. 设置 app.json 和全局样式

### 阶段二：数据层实现
1. 实现 `utils/level-data.js`（关卡数据）
2. 实现 `utils/game-logic.js`（游戏逻辑）
   - pourWater 函数
   - checkWin 函数
   - 辅助函数

### 阶段三：主页开发
1. 创建关卡选择页面布局
2. 实现关卡列表渲染
3. 实现跳转逻辑

### 阶段四：游戏页开发
1. 创建游戏页面布局
2. 实现试管渲染
3. 实现点击交互
4. 实现倒水逻辑调用
5. 实现胜利判断

### 阶段五：优化完善
1. 添加倒水动画
2. 添加胜利提示
3. 样式美化
4. 测试和修复bug

## 预期成果

完成后将得到：

1. 功能完整的水排序游戏微信小程序
2. 5 个可玩的关卡
3. 流畅的交互体验
4. 清晰的代码结构，便于后续扩展

## 后续扩展方向

如果需要增强功能，可以考虑：

- 添加撤销功能（记录操作历史）
- 添加提示功能（提示下一步操作）
- 添加关卡解锁系统
- 添加计分/星级评价
- 添加音效和背景音乐
- 添加更多关卡
- 添加难度选择
- 添加排行榜（需要后端支持）

---

**设计完成时间**: 2026-02-28
**设计者**: Claude Code
**版本**: v1.0
