# 水排序游戏移动端部署实施计划

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** 将水排序游戏优化为移动端友好版本，部署到 GitHub Pages，并提供完整的注册部署指导文档

**Architecture:** 在现有单文件 HTML 游戏基础上，添加移动端优化（触摸支持、响应式设计、性能优化），创建 PWA 配置和图标，编写详细的用户指导文档

**Tech Stack:** HTML5, CSS3, JavaScript ES6+, PWA, GitHub Pages

---

## Task 1: 优化移动端 HTML - Meta 标签和基础配置

**Files:**
- Modify: `water-sort-game.html:1-10`

**Step 1: 添加移动端 Meta 标签**

在 `<head>` 标签中添加移动端优化的 meta 标签：

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="theme-color" content="#667eea">
<meta name="description" content="经典的水排序益智游戏，在线免费玩">
<link rel="manifest" href="manifest.json">
<link rel="apple-touch-icon" href="icon-192.png">
```

在原有的 viewport meta 标签后面添加这些标签。

**Step 2: 验证 HTML 结构**

在浏览器中打开 `water-sort-game.html`，按 F12 打开开发者工具，切换到移动端视图（iPhone 12 Pro），确认：
- 页面正常显示
- 无控制台错误
- viewport 设置生效

**Step 3: Commit**

```bash
git add water-sort-game.html
git commit -m "feat: add mobile meta tags and PWA support"
```

---

## Task 2: 优化移动端 CSS - 禁用默认行为

**Files:**
- Modify: `water-sort-game.html:9-13` (style 标签内)

**Step 1: 添加移动端触摸优化样式**

在 `<style>` 标签的开头（`* { margin: 0; }` 之前）添加：

```css
/* 移动端触摸优化 */
* {
  -webkit-tap-highlight-color: transparent;
  -webkit-touch-callout: none;
  -webkit-user-select: none;
  user-select: none;
}

html, body {
  overscroll-behavior: none;
  -webkit-overflow-scrolling: touch;
}
```

**Step 2: 更新 body 样式**

找到 `body {` 样式规则，添加：

```css
body {
  /* 原有样式保持不变 */
  touch-action: pan-y;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* 添加安全区域支持 */
  padding-top: max(20px, env(safe-area-inset-top));
  padding-bottom: max(20px, env(safe-area-inset-bottom));
  padding-left: max(20px, env(safe-area-inset-left));
  padding-right: max(20px, env(safe-area-inset-right));
}
```

**Step 3: 在移动端测试**

使用 Chrome DevTools 移动端模拟器测试：
- iPhone SE (375x667)
- iPhone 12 Pro (390x844)
- Samsung Galaxy S20 (360x800)

确认：
- 无法选择文字
- 长按无菜单弹出
- 点击无蓝色高亮

**Step 4: Commit**

```bash
git add water-sort-game.html
git commit -m "feat: disable mobile browser default behaviors"
```

---

## Task 3: 优化移动端 CSS - 响应式断点

**Files:**
- Modify: `water-sort-game.html:268-292` (响应式设计部分)

**Step 1: 替换现有的响应式 CSS**

找到 `/* 响应式设计 */` 注释部分，完全替换为：

```css
/* 响应式设计 */
/* 超小屏手机 (<375px) */
@media (max-width: 374px) {
  h1 {
    font-size: 28px;
    margin-bottom: 15px;
  }

  .creator-info {
    font-size: 12px;
    padding: 6px 12px;
    top: 5px;
    right: 10px;
  }

  .level-btn {
    width: 55px;
    height: 55px;
    font-size: 24px;
  }

  #game-area {
    padding: 15px;
  }

  .tube {
    width: 48px;
    height: 192px;
  }

  #tubes-container {
    gap: 12px;
  }

  #level-display {
    font-size: 18px;
  }

  #restart-btn {
    padding: 8px 16px;
    font-size: 14px;
  }
}

/* 小屏手机 (375-414px) */
@media (min-width: 375px) and (max-width: 414px) {
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
    width: 52px;
    height: 208px;
  }

  #tubes-container {
    gap: 15px;
  }
}

/* 大屏手机 (>414px) - 保持原有样式 */
@media (min-width: 415px) and (max-width: 768px) {
  .tube {
    width: 58px;
    height: 232px;
  }
}

/* 横屏模式 */
@media (orientation: landscape) and (max-height: 500px) {
  h1 {
    font-size: 24px;
    margin-bottom: 10px;
  }

  #game-area {
    padding: 15px;
  }

  #tubes-container {
    max-height: 50vh;
    overflow-y: auto;
    padding: 10px;
  }

  .tube {
    width: 50px;
    height: 180px;
  }

  .modal-content {
    padding: 25px;
  }

  .modal-content h2 {
    font-size: 28px;
    margin-bottom: 20px;
  }
}

/* 平板和大屏 */
@media (min-width: 768px) {
  #app {
    max-width: 1000px;
  }

  .tube {
    width: 65px;
    height: 260px;
  }
}
```

**Step 2: 测试所有断点**

在 Chrome DevTools 中测试以下设备：
- iPhone SE (375x667) - 竖屏
- iPhone SE (667x375) - 横屏
- iPhone 12 (390x844)
- iPad Mini (768x1024)
- Samsung Galaxy S20 (360x800)

确认每个断点的显示效果正常。

**Step 3: Commit**

```bash
git add water-sort-game.html
git commit -m "feat: add responsive breakpoints for all mobile devices"
```

---

## Task 4: 优化触摸反馈动画

**Files:**
- Modify: `water-sort-game.html` (CSS 部分)

**Step 1: 增强试管触摸反馈**

找到 `.tube` 样式规则，确保包含这些属性：

```css
.tube {
  /* 原有样式保持 */
  will-change: transform;
  transform: translateZ(0);
  -webkit-tap-highlight-color: transparent;
}

.tube:active {
  transform: scale(0.95) translateY(-5px);
  transition: transform 0.1s ease;
}
```

**Step 2: 增强按钮触摸反馈**

找到所有按钮样式（`.level-btn`, `#restart-btn`, `.modal-buttons button`），添加 active 状态：

```css
.level-btn:active,
#restart-btn:active,
.modal-buttons button:active {
  transform: scale(0.92);
  opacity: 0.85;
  transition: transform 0.1s ease, opacity 0.1s ease;
}
```

**Step 3: 优化水层动画**

找到 `.water-layer` 样式，添加 GPU 加速：

```css
.water-layer {
  width: 100%;
  transition: all 0.3s ease;
  will-change: transform;
  transform: translateZ(0);
}
```

**Step 4: 在手机上测试触摸反馈**

如果有真机，使用真机测试；否则在模拟器中测试：
- 点击试管时有缩放反馈
- 点击按钮时有缩放和透明度变化
- 动画流畅无卡顿

**Step 5: Commit**

```bash
git add water-sort-game.html
git commit -m "feat: enhance touch feedback animations"
```

---

## Task 5: 添加加载屏幕

**Files:**
- Modify: `water-sort-game.html:296-322` (body 内容部分)

**Step 1: 添加加载屏幕 HTML**

在 `<body>` 标签后，`<div id="app">` 之前添加：

```html
<!-- 加载屏幕 -->
<div id="loading-screen" class="loading-screen">
  <div class="spinner"></div>
  <p>游戏加载中...</p>
</div>
```

**Step 2: 添加加载屏幕 CSS**

在 `/* 工具类 */` 部分之前添加：

```css
/* 加载屏幕样式 */
.loading-screen {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  z-index: 9999;
  transition: opacity 0.3s ease;
}

.loading-screen.fade-out {
  opacity: 0;
  pointer-events: none;
}

.spinner {
  width: 50px;
  height: 50px;
  border: 4px solid rgba(255, 255, 255, 0.3);
  border-top-color: white;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.loading-screen p {
  color: white;
  font-size: 18px;
  margin-top: 20px;
  font-weight: 500;
}
```

**Step 3: 添加加载屏幕 JavaScript**

在 `window.onload` 函数的开头添加：

```javascript
window.onload = function() {
  // 隐藏加载屏幕
  const loadingScreen = document.getElementById('loading-screen');
  loadingScreen.classList.add('fade-out');
  setTimeout(() => {
    loadingScreen.style.display = 'none';
  }, 300);

  console.log('游戏加载完成');
  // ... 原有代码继续
};
```

**Step 4: 测试加载屏幕**

刷新浏览器，确认：
- 页面加载时显示转圈动画
- 加载完成后平滑淡出
- 游戏正常显示

**Step 5: Commit**

```bash
git add water-sort-game.html
git commit -m "feat: add loading screen with spinner animation"
```

---

## Task 6: 防止双击缩放

**Files:**
- Modify: `water-sort-game.html` (JavaScript 部分)

**Step 1: 添加防双击缩放代码**

在 `// ============ 游戏状态 ============` 之前添加：

```javascript
// ============ 移动端优化 ============
// 防止双击缩放
let lastTouchEnd = 0;
document.addEventListener('touchend', function(event) {
  const now = Date.now();
  if (now - lastTouchEnd <= 300) {
    event.preventDefault();
  }
  lastTouchEnd = now;
}, false);

// 防止双指缩放
document.addEventListener('gesturestart', function(event) {
  event.preventDefault();
});
```

**Step 2: 在移动设备上测试**

使用移动端模拟器或真机测试：
- 双击屏幕不会缩放
- 双指缩放被禁用
- 游戏交互正常

**Step 3: Commit**

```bash
git add water-sort-game.html
git commit -m "feat: prevent double-tap zoom on mobile"
```

---

## Task 7: 创建 PWA Manifest 文件

**Files:**
- Create: `manifest.json`

**Step 1: 创建 manifest.json**

创建新文件 `manifest.json`，内容如下：

```json
{
  "name": "水排序游戏",
  "short_name": "水排序",
  "description": "经典的水排序益智游戏，轻松休闲，适合所有年龄",
  "start_url": "./index.html",
  "display": "standalone",
  "background_color": "#667eea",
  "theme_color": "#667eea",
  "orientation": "portrait-primary",
  "icons": [
    {
      "src": "icon-192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any maskable"
    },
    {
      "src": "icon-512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any maskable"
    }
  ],
  "categories": ["games", "entertainment"],
  "lang": "zh-CN"
}
```

**Step 2: 验证 manifest.json**

在浏览器中打开 `water-sort-game.html`，打开 DevTools：
- 切换到 Application 标签
- 点击左侧 Manifest
- 确认配置正确显示（图标会显示为缺失，这是正常的）

**Step 3: Commit**

```bash
git add manifest.json
git commit -m "feat: add PWA manifest configuration"
```

---

## Task 8: 重命名主文件为 index.html

**Files:**
- Rename: `water-sort-game.html` → `index.html`

**Step 1: 复制并重命名文件**

```bash
cp water-sort-game.html index.html
```

**Step 2: 更新 manifest.json**

manifest.json 中的 start_url 已经是 `./index.html`，无需修改。

**Step 3: 验证新文件**

在浏览器中打开 `index.html`，确认：
- 游戏正常运行
- 所有功能正常
- 背景图片正常显示

**Step 4: Commit**

```bash
git add index.html
git commit -m "feat: create index.html for GitHub Pages"
```

注意：保留 `water-sort-game.html` 作为备份，稍后会在 GitHub 部署时决定是否删除。

---

## Task 9: 创建图标占位符（临时）

**Files:**
- Create: `icon-192.png`
- Create: `icon-512.png`

**Step 1: 创建临时图标说明文件**

创建 `ICONS-TODO.md`：

```markdown
# 图标文件待创建

需要创建以下图标文件：

1. **icon-192.png** (192x192px)
   - 用于 PWA 应用图标
   - 建议：试管图案，紫色渐变背景

2. **icon-512.png** (512x512px)
   - 用于 PWA 应用图标（高清）
   - 与 192px 版本设计一致

## 创建方法

### 方法 1：使用在线工具
访问 https://www.favicon-generator.org/ 或 https://realfavicongenerator.net/

### 方法 2：使用设计软件
使用 Figma、Photoshop 或其他设计工具创建

### 方法 3：使用 Emoji
将游戏中的试管 emoji 🧪 转换为图标：
- 访问 https://emoji.gg/
- 搜索 test tube
- 下载并调整大小

## 临时方案

在图标创建之前，可以：
1. 注释掉 manifest.json 中的 icons 部分
2. 或使用简单的纯色图标占位

创建完成后替换即可。
```

**Step 2: Commit**

```bash
git add ICONS-TODO.md
git commit -m "docs: add icon creation instructions"
```

注意：真实的图标文件需要用户自己创建或我们后续帮助生成。现在先跳过，不影响核心功能。

---

## Task 10: 创建 GitHub 注册指导文档

**Files:**
- Create: `docs/guides/GitHub注册指导.md`

**Step 1: 创建文档目录**

```bash
mkdir -p docs/guides
```

**Step 2: 创建注册指导文档**

创建 `docs/guides/GitHub注册指导.md`：

```markdown
# GitHub 账号注册指导

本文档将指导你一步一步注册 GitHub 账号，以便部署你的水排序游戏。

---

## 第一步：访问 GitHub

1. 打开浏览器（推荐使用 Chrome 或 Edge）
2. 访问网址：https://github.com
3. 你会看到 GitHub 的首页

---

## 第二步：开始注册

1. 点击右上角的 **"Sign up"** 按钮（注册）
2. 或者点击页面中间的绿色 **"Sign up for GitHub"** 按钮

---

## 第三步：填写注册信息

### 3.1 输入邮箱地址
- 输入你的邮箱地址（建议使用常用邮箱）
- 邮箱用于接收验证码和重要通知
- 点击 **"Continue"**（继续）

### 3.2 创建密码
- 输入一个安全的密码
- 密码要求：至少 15 个字符，或至少 8 个字符且包含数字和小写字母
- 点击 **"Continue"**

### 3.3 选择用户名
- 输入一个用户名（username）
- 这个用户名会出现在你的游戏网址中：`https://用户名.github.io/`
- 用户名要求：
  - 只能包含字母、数字和连字符 `-`
  - 不能以连字符开头或结尾
  - 不能包含中文
- 示例：`zhangsan`、`game-master`、`happy-coder123`
- 点击 **"Continue"**

### 3.4 邮件通知设置（可选）
- 选择是否接收 GitHub 的产品更新和公告
- 输入 `y`（是）或 `n`（否）
- 点击 **"Continue"**

---

## 第四步：验证你是人类

1. 点击 **"Verify"**（验证）按钮
2. 完成验证码挑战（可能需要：）
   - 选择图片中的特定物体
   - 拖动滑块
   - 或其他验证方式
3. 完成后点击 **"Create account"**（创建账号）

---

## 第五步：验证邮箱

1. GitHub 会向你的邮箱发送一封验证邮件
2. 打开你的邮箱，查找来自 GitHub 的邮件
   - 邮件主题类似："[GitHub] Please verify your email address"
3. 在邮件中找到 **验证码**（8 位数字）
4. 回到 GitHub 页面，输入验证码
5. 点击验证

> **提示**：如果没收到邮件，请：
> - 检查垃圾邮件文件夹
> - 等待 1-2 分钟
> - 点击"重新发送邮件"

---

## 第六步：完成个性化设置

验证邮箱后，GitHub 会询问一些问题（可以跳过）：

1. **你是学生还是老师？**
   - 选择 "Student"、"Teacher" 或 "None of the above"（以上都不是）
   - 或点击 **"Skip personalization"**（跳过个性化）

2. **你对什么感兴趣？**
   - 可以选择一些标签，或直接跳过

3. **你想要什么样的体验？**
   - 选择 "Collaborative coding"、"Learning" 等，或跳过

---

## 第七步：选择免费计划

1. GitHub 会显示订阅计划页面
2. 选择 **"Continue for free"**（继续使用免费版）
3. 免费版完全够用，无需付费

---

## 完成！

🎉 恭喜！你已经成功注册了 GitHub 账号！

现在你可以：
- 访问你的个人主页：`https://github.com/你的用户名`
- 继续下一步：部署你的水排序游戏

---

## 常见问题

### Q1: 用户名可以修改吗？
可以，但不建议频繁修改。修改步骤：
1. 点击右上角头像
2. Settings（设置）
3. Account（账户）
4. Change username（更改用户名）

### Q2: 忘记密码怎么办？
1. 在登录页面点击 "Forgot password?"
2. 输入邮箱地址
3. 按照邮件指引重置密码

### Q3: 邮箱验证码一直收不到？
1. 检查垃圾邮件文件夹
2. 尝试使用其他邮箱重新注册
3. 联系 GitHub 客服

### Q4: 可以使用中文用户名吗？
不可以，GitHub 用户名只支持英文字母、数字和连字符。

---

## 下一步

注册完成后，请继续阅读：
- 📖 [部署教程](./部署教程.md) - 学习如何部署游戏
- 📖 [分享指南](./分享指南.md) - 学习如何分享给朋友

---

*文档创建时间：2026-02-28*
```

**Step 3: Commit**

```bash
git add docs/guides/GitHub注册指导.md
git commit -m "docs: add GitHub registration guide"
```

---

## Task 11: 创建部署教程文档

**Files:**
- Create: `docs/guides/部署教程.md`

**Step 1: 创建部署教程文档**

创建 `docs/guides/部署教程.md`：

```markdown
# 水排序游戏部署教程

本教程将指导你把水排序游戏部署到 GitHub Pages，让朋友可以通过网址直接玩。

---

## 前提条件

- ✅ 已注册 GitHub 账号（如果还没有，请先阅读 [GitHub注册指导](./GitHub注册指导.md)）
- ✅ 已登录 GitHub 账号
- ✅ 准备好游戏文件（index.html 和 mega-background.png）

---

## 部署方式选择

我们提供两种部署方式，**推荐方式一**（更简单）：

- **方式一**：网页界面上传（简单，适合新手）⭐ 推荐
- **方式二**：Git 命令行（需要安装 Git）

---

## 方式一：网页界面上传（推荐）

### 第一步：创建仓库

1. 登录 GitHub（https://github.com）

2. 点击右上角的 **"+"** 号，选择 **"New repository"**（新建仓库）

3. 填写仓库信息：
   - **Repository name**（仓库名称）：`water-sort-puzzle`
     - 这个名字会出现在网址中
     - 只能用小写字母、数字和连字符
   - **Description**（描述）：`水排序游戏 - 在线益智游戏`
   - **Public**（公开）：选择这个（必须选择公开才能用 GitHub Pages）
   - **Add a README file**：不勾选
   - **Add .gitignore**：选择 None
   - **Choose a license**：选择 None

4. 点击绿色按钮 **"Create repository"**（创建仓库）

### 第二步：上传游戏文件

1. 仓库创建成功后，你会看到一个空仓库页面

2. 点击 **"uploading an existing file"** 链接
   - 或者直接点击 **"Add file"** → **"Upload files"**

3. 上传文件：
   - 方法 1：拖拽文件到页面中
   - 方法 2：点击 "choose your files"，选择文件

4. 需要上传的文件：
   - ✅ `index.html`（优化后的游戏文件）
   - ✅ `mega-background.png`（背景图片）
   - ✅ `manifest.json`（PWA 配置文件）
   - ⭕ `icon-192.png`（可选，如果你已创建）
   - ⭕ `icon-512.png`（可选，如果你已创建）

5. 在 "Commit changes" 区域：
   - 标题自动填写为 "Add files via upload"
   - 可以修改为："初始提交：上传水排序游戏"

6. 点击绿色按钮 **"Commit changes"**（提交更改）

### 第三步：启用 GitHub Pages

1. 上传完成后，点击仓库顶部的 **"Settings"**（设置）标签

2. 在左侧菜单中，向下滚动找到 **"Pages"**

3. 在 "Build and deployment" 部分：
   - **Source**（源）：选择 **"Deploy from a branch"**
   - **Branch**（分支）：选择 **"main"** 和 **"/ (root)"**
   - 点击 **"Save"**（保存）

4. 等待 1-2 分钟，页面会显示：
   ```
   Your site is live at https://你的用户名.github.io/water-sort-puzzle/
   ```

5. 点击这个网址，查看你的游戏！

### 第四步：测试游戏

1. 在电脑浏览器中打开网址，测试游戏是否正常运行

2. 用手机浏览器打开同样的网址，测试移动端效果

3. 确认以下功能：
   - ✅ 页面正常显示
   - ✅ 背景图片正常加载
   - ✅ 关卡按钮可以点击
   - ✅ 试管可以正常倒水
   - ✅ 胜利弹窗正常显示

---

## 方式二：Git 命令行

### 前提条件

- 已安装 Git（下载地址：https://git-scm.com/）
- 已配置 Git 用户信息

### 第一步：在 GitHub 创建空仓库

1. 登录 GitHub

2. 点击右上角的 **"+"** → **"New repository"**

3. 填写信息（与方式一相同）：
   - Repository name: `water-sort-puzzle`
   - Description: `水排序游戏`
   - Public
   - 不勾选任何选项

4. 点击 **"Create repository"**

### 第二步：本地初始化并推送

1. 打开命令行工具（Windows: Git Bash，Mac/Linux: Terminal）

2. 进入游戏文件所在目录：
   ```bash
   cd D:\AI\skill
   # 或你的实际路径
   ```

3. 初始化 Git 仓库：
   ```bash
   git init
   ```

4. 添加文件：
   ```bash
   git add index.html mega-background.png manifest.json
   ```

5. 提交更改：
   ```bash
   git commit -m "初始提交：水排序游戏"
   ```

6. 设置主分支名称：
   ```bash
   git branch -M main
   ```

7. 添加远程仓库：
   ```bash
   git remote add origin https://github.com/你的用户名/water-sort-puzzle.git
   ```

   ⚠️ 注意：把 `你的用户名` 替换为你的实际 GitHub 用户名！

8. 推送到 GitHub：
   ```bash
   git push -u origin main
   ```

9. 首次推送会要求输入 GitHub 用户名和密码
   - 用户名：你的 GitHub 用户名
   - 密码：需要使用 **Personal Access Token**（不是登录密码）

   生成 Token 的方法：
   - 访问：https://github.com/settings/tokens
   - 点击 "Generate new token" → "Generate new token (classic)"
   - 勾选 "repo" 权限
   - 点击 "Generate token"
   - 复制 token 并保存（只显示一次！）

### 第三步：启用 GitHub Pages

与方式一的第三步相同。

---

## 更新游戏内容

如果你想修改游戏（比如添加新关卡），可以这样操作：

### 使用网页界面更新

1. 访问你的仓库页面
2. 点击要修改的文件（如 index.html）
3. 点击右侧的铅笔图标（编辑）
4. 修改内容
5. 滚动到底部，点击 "Commit changes"
6. 等待 1-2 分钟，刷新游戏网址

### 使用 Git 命令行更新

```bash
# 修改文件后
git add index.html
git commit -m "添加新关卡"
git push
```

---

## 常见问题

### Q1: 部署后显示 404 Not Found？

**可能原因**：
1. GitHub Pages 还没生成完毕，等待 2-3 分钟
2. 没有 index.html 文件，检查文件名是否正确
3. 仓库设置为 Private，需要改为 Public

**解决方法**：
- 等待 3-5 分钟后刷新
- 检查 Settings → Pages 是否显示绿色的 "Your site is published"
- 确认仓库是 Public（Settings → General → Change visibility）

### Q2: 背景图片不显示？

**可能原因**：
1. 图片文件名错误
2. 图片路径错误
3. 图片文件太大，加载缓慢

**解决方法**：
- 确认图片文件名为 `mega-background.png`（注意大小写）
- 确认图片文件已上传到仓库根目录
- 打开浏览器开发者工具（F12）→ Network，查看图片加载状态

### Q3: 手机访问很慢？

**可能原因**：
- 背景图片太大
- 网络问题
- GitHub CDN 在某些地区较慢

**解决方法**：
- 压缩背景图片（使用 tinypng.com）
- 使用 CDN 加速（可选）
- 等待一会儿，首次加载会慢一些

### Q4: 如何使用自己的域名？

1. 购买域名（如 game.yourdomain.com）
2. 在仓库根目录创建 `CNAME` 文件
3. 文件内容填写你的域名
4. 在域名管理处添加 CNAME 记录，指向 `你的用户名.github.io`

### Q5: 能删除仓库吗？

可以，但会导致游戏网址失效：
1. Settings → General
2. 滚动到最底部 "Danger Zone"
3. 点击 "Delete this repository"
4. 输入仓库名称确认删除

---

## 性能优化建议

### 1. 压缩图片
使用在线工具压缩背景图片：
- https://tinypng.com/
- https://squoosh.app/

目标：将图片大小控制在 500KB 以内

### 2. 启用浏览器缓存
在 index.html 的 `<head>` 中添加：
```html
<meta http-equiv="Cache-Control" content="max-age=31536000">
```

### 3. 使用 CDN（可选）
可以使用 jsDelivr 加速：
```
https://cdn.jsdelivr.net/gh/你的用户名/water-sort-puzzle@main/index.html
```

---

## 下一步

部署完成后，请继续阅读：
- 📖 [分享指南](./分享指南.md) - 学习如何分享给朋友

---

## 需要帮助？

如果遇到问题：
1. 仔细阅读错误提示
2. 检查控制台（F12 → Console）
3. 参考本文档的"常见问题"部分
4. 在仓库中提交 Issue

---

*文档创建时间：2026-02-28*
*最后更新：2026-02-28*
```

**Step 2: Commit**

```bash
git add docs/guides/部署教程.md
git commit -m "docs: add deployment tutorial"
```

---

## Task 12: 创建分享指南文档

**Files:**
- Create: `docs/guides/分享指南.md`

**Step 1: 创建分享指南文档**

创建 `docs/guides/分享指南.md`：

```markdown
# 游戏分享指南

游戏部署成功后，你可以通过多种方式分享给朋友。本指南将教你最有效的分享方法。

---

## 你的游戏网址

部署成功后，你的游戏网址是：

```
https://你的用户名.github.io/water-sort-puzzle/
```

请替换 `你的用户名` 为你的实际 GitHub 用户名。

**示例**：
- 如果用户名是 `zhangsan`
- 游戏网址就是：`https://zhangsan.github.io/water-sort-puzzle/`

---

## 方式一：直接分享链接（最简单）

### 微信分享

1. 复制你的游戏网址
2. 打开微信
3. 找到想分享的好友或群聊
4. 粘贴网址并发送
5. 朋友点击链接即可打开游戏

**提示**：
- 微信会自动生成链接预览
- 朋友可以直接在微信内置浏览器中玩

### QQ 分享

1. 复制游戏网址
2. 打开 QQ
3. 发送给好友或群聊
4. 朋友点击链接即可打开

### 短信分享

1. 打开手机短信
2. 输入："我做了一个水排序游戏，快来玩吧！"
3. 粘贴游戏网址
4. 发送给朋友

---

## 方式二：生成二维码（推荐）

二维码可以让朋友用手机扫一扫直接打开游戏，非常方便！

### 使用在线工具生成

#### 工具 1：草料二维码（推荐，中文界面）

1. 访问：https://cli.im/
2. 在输入框中粘贴你的游戏网址
3. 点击"生成二维码"
4. 右键点击二维码图片 → "图片另存为"
5. 保存到本地

#### 工具 2：QR Code Generator（国际站）

1. 访问：https://www.qr-code-generator.com/
2. 选择 "URL" 类型
3. 粘贴你的游戏网址
4. 点击 "Create QR Code"
5. 点击 "Download" 下载二维码

#### 工具 3：二维工坊（功能丰富）

1. 访问：https://www.2weima.com/
2. 选择"网址二维码"
3. 输入游戏网址
4. 可以自定义二维码样式（颜色、logo 等）
5. 下载二维码

### 美化二维码（可选）

你可以给二维码添加一些美化：

1. **添加 Logo**：在二维码中心添加游戏图标
2. **改变颜色**：使用蓝色、紫色等与游戏主题匹配的颜色
3. **添加文字**：在二维码下方添加"扫码玩游戏"等文字

推荐工具：
- https://www.wwei.cn/ （微微二维码，可添加 logo）
- https://www.liantu.com/ （联图二维码，在线美化）

### 分享二维码

下载二维码后，你可以：

1. **发送图片**
   - 微信：直接发送二维码图片
   - QQ：发送二维码图片
   - 朋友扫码即可打开游戏

2. **打印出来**
   - 打印二维码贴在墙上
   - 做成名片
   - 放在展示区域

3. **社交媒体**
   - 发朋友圈配上二维码
   - 发微博
   - 发小红书

---

## 方式三：短链接（可选）

如果你觉得网址太长，可以生成短链接：

### 短链接工具

#### 新浪短链接

1. 访问：https://www.sina.lt/
2. 粘贴你的游戏网址
3. 点击"缩短"
4. 复制生成的短链接（如：https://sina.lt/fPxH）

#### 百度短链接

1. 访问：https://dwz.cn/
2. 输入游戏网址
3. 点击"缩短网址"
4. 复制生成的短链接

#### Bitly（国际站）

1. 访问：https://bitly.com/
2. 注册免费账号
3. 输入游戏网址
4. 点击 "Create"
5. 复制短链接

**短链接的好处**：
- 更短，更容易记忆
- 看起来更专业
- 可以追踪点击次数（某些服务）

---

## 方式四：社交媒体推广

### 朋友圈

```
文案示例：

🎮 我做了一个水排序游戏！
💧 超解压的益智小游戏
📱 手机电脑都能玩
🆓 完全免费，无广告

快来挑战吧！👇
[游戏网址或二维码]

#水排序游戏 #益智游戏 #小游戏
```

### 微博

```
文案示例：

【自制小游戏分享】💙

水排序游戏，简单又上瘾！
试试你能过几关？🧪

✅ 手机电脑都能玩
✅ 免费无广告
✅ 5个关卡等你挑战

游戏链接：[你的网址]

#小游戏 #水排序 #益智游戏
```

### 小红书

```
标题：自己做了一个超解压的水排序小游戏！

正文：
姐妹们！我做了一个超好玩的水排序游戏 🧪

就是那种把不同颜色的水倒来倒去，
最后让每个试管只有一种颜色的游戏！

💙 特点：
✓ 完全免费
✓ 不用下载APP
✓ 手机电脑都能玩
✓ 没有广告打扰
✓ 5个关卡挑战

真的超解压！无聊的时候玩玩特别舒服~

扫码就能玩 👉 [二维码图片]

#水排序游戏 #小游戏推荐 #益智游戏
#自制游戏 #网页游戏
```

---

## 方式五：添加到手机桌面

教朋友把游戏添加到手机桌面，就像 APP 一样：

### iPhone / iPad (Safari)

1. 用 Safari 浏览器打开游戏网址
2. 点击底部的"分享"按钮（向上箭头图标）
3. 向下滚动，找到"添加到主屏幕"
4. 点击"添加"
5. 游戏图标会出现在桌面上

### Android (Chrome)

1. 用 Chrome 浏览器打开游戏网址
2. 点击右上角的 ⋮ 菜单
3. 选择"添加到主屏幕"
4. 点击"添加"
5. 游戏图标会出现在桌面上

**好处**：
- 像 APP 一样方便
- 全屏游戏体验
- 下次打开更快

---

## 分享技巧

### 1. 配上精美截图

截取游戏画面，让朋友更有兴趣：
- 关卡选择界面
- 游戏进行中的画面
- 胜利弹窗

### 2. 制作宣传图

使用 Canva、创客贴等工具制作：
- 游戏名称 + 二维码
- 简短说明 + 游戏特点
- 精美背景 + 游戏截图

### 3. 录制演示视频

录制一段游戏视频：
- 展示如何玩
- 过关的成就感
- 发布到抖音、快手、B站等

### 4. 举办小比赛

组织朋友们一起玩：
- 看谁用最少步数过关
- 看谁最快通关所有关卡
- 增加趣味性和传播性

---

## 监控分享效果（可选）

### 添加访问统计

在 `index.html` 的 `</head>` 之前添加 Google Analytics：

```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

这样你就能看到：
- 有多少人访问了游戏
- 访问来自哪里
- 访问时间分布

---

## 示例分享文案合集

### 简洁版
```
分享一个我做的小游戏：水排序 🧪
点击就能玩：[网址]
```

### 详细版
```
🎮 水排序游戏 - 休闲益智小游戏

💧 玩法：把混合的彩色水倒到不同试管，
   最后让每个试管只有一种颜色

✨ 特点：
   • 手机电脑都能玩
   • 完全免费无广告
   • 5个关卡等你挑战
   • 越玩越上瘾

快来试试吧！👇
[游戏网址或二维码]
```

### 挑战版
```
我做了个水排序游戏，已经卡在第3关了 😭
你能帮我通关吗？

游戏地址：[网址]

看看谁能最快通关！⏱️
```

---

## 常见问题

### Q1: 朋友说打不开链接？

**可能原因**：
- 网址复制不完整
- 网络问题
- 浏览器兼容性

**解决方法**：
- 重新发送完整网址
- 换个浏览器试试（Chrome、Safari）
- 用二维码方式分享

### Q2: 微信分享没有预览图？

GitHub Pages 默认没有预览图。如果需要，可以：
1. 添加 Open Graph 标签
2. 在 index.html 的 `<head>` 中添加：
```html
<meta property="og:title" content="水排序游戏">
<meta property="og:description" content="休闲益智小游戏，来挑战吧！">
<meta property="og:image" content="https://你的网址/preview.png">
```
3. 上传一张预览图（preview.png）

### Q3: 二维码扫不出来？

**可能原因**：
- 二维码图片太小或模糊
- 光线不好
- 二维码损坏

**解决方法**：
- 重新生成更大的二维码
- 提高图片清晰度
- 调整扫描角度和距离

---

## 创意分享方式

1. **制作游戏攻略视频**
   - 录制通关教程
   - 分享到抖音、B站
   - 在视频简介放链接

2. **做成趣味挑战**
   - "30秒通关挑战"
   - "最少步数挑战"
   - 增加游戏传播性

3. **设计周边**
   - 打印二维码贴纸
   - 制作游戏主题壁纸
   - 设计表情包

4. **社群推广**
   - 在游戏群、QQ群分享
   - 在论坛发帖
   - 在知乎、豆瓣分享

---

祝你的游戏被更多人喜欢！🎉

---

*文档创建时间：2026-02-28*
```

**Step 2: Commit**

```bash
git add docs/guides/分享指南.md
git commit -m "docs: add sharing guide"
```

---

## Task 13: 创建主 README 文档

**Files:**
- Create: `README.md`

**Step 1: 创建 README.md**

创建项目根目录的 `README.md`：

```markdown
# 水排序游戏 🧪

一个简洁优雅的网页版水排序益智游戏，支持移动端和桌面端。

![游戏截图](https://via.placeholder.com/800x400/667eea/ffffff?text=Water+Sort+Puzzle)

## 🎮 在线试玩

👉 [点击这里开始游戏](https://你的用户名.github.io/water-sort-puzzle/)

或扫描二维码：

![二维码](https://via.placeholder.com/200x200/667eea/ffffff?text=QR+Code)

## ✨ 游戏特点

- 🎯 **经典玩法**：将混合的彩色水倒入不同试管，让每个试管只包含一种颜色
- 📱 **移动优化**：完美支持手机、平板和电脑
- 🎨 **精美设计**：渐变背景、流畅动画、现代化 UI
- 🆓 **完全免费**：无广告、无内购、无需下载
- 🚀 **即点即玩**：打开网页就能玩，无需安装

## 🕹️ 游戏玩法

1. 点击一个试管选中它
2. 再点击另一个试管，将水倒过去
3. 倒水规则：
   - 只能将水倒到空试管或顶部颜色相同的试管
   - 一次倒水会倒出所有连续相同颜色的水
4. 目标：让每个试管只包含一种颜色，或者为空

## 📋 关卡列表

- **关卡 1**：3 种颜色，5 个试管 - 简单入门
- **关卡 2**：3 种颜色，5 个试管 - 稍有挑战
- **关卡 3**：4 种颜色，6 个试管 - 中等难度
- **关卡 4**：4 种颜色，6 个试管 - 需要思考
- **关卡 5**：5 种颜色，7 个试管 - 高手挑战

## 🛠️ 技术栈

- **前端**：原生 HTML5 + CSS3 + JavaScript (ES6+)
- **部署**：GitHub Pages
- **特性**：
  - PWA 支持（可添加到主屏幕）
  - 响应式设计（支持所有设备）
  - 触摸优化（手机体验流畅）
  - GPU 加速动画（60fps）

## 📱 移动端体验

### 添加到主屏幕

#### iPhone / iPad
1. 用 Safari 打开游戏
2. 点击底部分享按钮
3. 选择"添加到主屏幕"

#### Android
1. 用 Chrome 打开游戏
2. 点击菜单（⋮）
3. 选择"添加到主屏幕"

添加后，游戏图标会出现在桌面上，就像原生 APP 一样！

## 📦 本地运行

```bash
# 克隆仓库
git clone https://github.com/你的用户名/water-sort-puzzle.git

# 进入目录
cd water-sort-puzzle

# 用浏览器打开 index.html
# Windows: start index.html
# Mac: open index.html
# Linux: xdg-open index.html
```

或者直接双击 `index.html` 文件。

## 🚀 部署指南

详细的部署教程请查看：
- [GitHub 注册指导](docs/guides/GitHub注册指导.md)
- [部署教程](docs/guides/部署教程.md)
- [分享指南](docs/guides/分享指南.md)

## 📝 文件结构

```
water-sort-puzzle/
├── index.html              # 主游戏文件
├── mega-background.png     # 背景图片
├── manifest.json           # PWA 配置
├── icon-192.png           # 应用图标（192x192）
├── icon-512.png           # 应用图标（512x512）
├── README.md              # 项目说明
├── ICONS-TODO.md          # 图标创建指南
└── docs/
    ├── plans/             # 设计和实施文档
    └── guides/            # 用户指导文档
        ├── GitHub注册指导.md
        ├── 部署教程.md
        └── 分享指南.md
```

## 🎨 自定义游戏

### 添加新关卡

编辑 `index.html`，在 `LEVELS` 数组中添加：

```javascript
{
  level: 6,
  tubes: [
    ['red', 'blue', 'green', 'yellow'],
    ['blue', 'green', 'yellow', 'red'],
    // ... 更多试管
    [],
    []
  ],
  capacity: 4
}
```

### 修改颜色

在 `COLORS` 对象中修改颜色值：

```javascript
const COLORS = {
  'red': '#FF6B6B',      // 可以改成任何颜色
  'blue': '#4ECDC4',
  // ...
};
```

### 更换背景

替换 `mega-background.png` 文件即可。

## 🐛 常见问题

### 游戏打不开？
- 检查浏览器是否支持（推荐 Chrome、Safari、Edge）
- 清除浏览器缓存后重试
- 检查网络连接

### 背景图片不显示？
- 确认 `mega-background.png` 文件存在
- 检查文件路径是否正确
- 尝试压缩图片大小（< 500KB）

### 手机上玩不流畅？
- 关闭其他后台应用
- 确保浏览器是最新版本
- 尝试清除浏览器缓存

更多问题请查看 [Issues](https://github.com/你的用户名/water-sort-puzzle/issues)

## 📄 开源协议

本项目采用 MIT 协议开源，你可以自由使用、修改和分享。

## 👨‍💻 作者

**制作者：张泽池**

如果你喜欢这个游戏，欢迎：
- ⭐ Star 这个项目
- 🔀 Fork 并改进
- 🐛 提交 Issue 和建议
- 📢 分享给更多人

## 🙏 致谢

- 游戏灵感来自经典的水排序益智游戏
- 使用 GitHub Pages 免费托管
- 感谢所有玩家的支持

---

**Enjoy the game!** 🎉

*最后更新：2026-02-28*
```

**Step 2: 用实际用户名替换占位符**

提醒用户：部署时需要把 README 中的 `你的用户名` 替换为实际的 GitHub 用户名。

**Step 3: Commit**

```bash
git add README.md
git commit -m "docs: add project README"
```

---

## Task 14: 创建 .gitignore 文件

**Files:**
- Create: `.gitignore`

**Step 1: 创建 .gitignore**

创建 `.gitignore` 文件：

```
# 操作系统文件
.DS_Store
Thumbs.db
desktop.ini

# 编辑器
.vscode/
.idea/
*.swp
*.swo
*~

# 临时文件
*.tmp
*.bak
*.log

# Node.js (如果将来添加构建工具)
node_modules/
npm-debug.log
yarn-error.log

# 备份文件
*.backup
backup/

# 压缩文件
*.zip
*.rar
*.7z
```

**Step 2: Commit**

```bash
git add .gitignore
git commit -m "chore: add gitignore file"
```

---

## Task 15: 最终检查和打包

**Files:**
- All project files

**Step 1: 检查文件完整性**

确认以下文件都已创建：
- ✅ `index.html`（优化后的游戏）
- ✅ `mega-background.png`（背景图片）
- ✅ `manifest.json`（PWA 配置）
- ✅ `README.md`（项目说明）
- ✅ `.gitignore`（Git 忽略规则）
- ✅ `ICONS-TODO.md`（图标创建指南）
- ✅ `docs/guides/GitHub注册指导.md`
- ✅ `docs/guides/部署教程.md`
- ✅ `docs/guides/分享指南.md`
- ✅ `docs/plans/2026-02-28-mobile-deployment-design.md`

**Step 2: 在浏览器中完整测试**

测试清单：
- [ ] 桌面浏览器：Chrome, Firefox, Edge
- [ ] 移动模拟器：iPhone SE, iPhone 12, iPad, Samsung Galaxy
- [ ] 所有关卡可以正常玩
- [ ] 触摸交互流畅
- [ ] 背景图片正常显示
- [ ] 加载屏幕正常工作
- [ ] 胜利弹窗正常显示
- [ ] 重新开始功能正常
- [ ] 横竖屏切换正常

**Step 3: 创建部署检查清单**

创建 `DEPLOYMENT-CHECKLIST.md`：

```markdown
# 部署检查清单

在部署到 GitHub Pages 之前，请确认以下事项：

## 文件准备

- [ ] `index.html` 已优化并测试通过
- [ ] `mega-background.png` 已压缩（< 500KB）
- [ ] `manifest.json` 已创建
- [ ] `README.md` 已更新（替换用户名）
- [ ] 图标文件已创建（icon-192.png, icon-512.png）或标记为待办

## 代码质量

- [ ] 没有控制台错误
- [ ] 所有功能正常工作
- [ ] 移动端触摸流畅
- [ ] 加载速度 < 3秒

## 文档完整性

- [ ] README 说明清晰
- [ ] 部署教程已编写
- [ ] 分享指南已编写
- [ ] GitHub 注册指导已编写

## GitHub 准备

- [ ] 已注册 GitHub 账号
- [ ] 已验证邮箱
- [ ] 准备好仓库名称：`water-sort-puzzle`

## 部署步骤

1. [ ] 在 GitHub 创建新仓库
2. [ ] 上传所有文件
3. [ ] 启用 GitHub Pages
4. [ ] 测试在线访问
5. [ ] 在手机上测试
6. [ ] 生成二维码
7. [ ] 分享给朋友测试

## 后续优化

- [ ] 创建游戏图标（icon-192.png, icon-512.png）
- [ ] 添加更多关卡
- [ ] 添加音效（可选）
- [ ] 添加步数统计（可选）
- [ ] 添加撤销功能（可选）

---

全部完成后，恭喜你成功部署了游戏！🎉
```

**Step 4: Commit**

```bash
git add DEPLOYMENT-CHECKLIST.md
git commit -m "docs: add deployment checklist"
```

**Step 5: 创建最终提交**

```bash
git add -A
git commit -m "feat: complete mobile optimization and documentation"
```

---

## 完成标准

项目完成时应具备：

### ✅ 功能完整性
- [x] 游戏在移动端完美运行
- [x] 触摸操作流畅自然
- [x] 横竖屏自动适配
- [x] 加载屏幕友好提示
- [x] 防止双击缩放和误触

### ✅ 文件完整性
- [x] 优化后的 `index.html`
- [x] PWA 配置 `manifest.json`
- [x] 完整的项目说明 `README.md`
- [x] Git 配置 `.gitignore`
- [x] 图标创建指南 `ICONS-TODO.md`

### ✅ 文档完整性
- [x] GitHub 注册指导文档
- [x] 详细的部署教程
- [x] 全面的分享指南
- [x] 部署检查清单

### ✅ 质量保证
- [x] 代码经过测试
- [x] 移动端体验优秀
- [x] 文档清晰易懂
- [x] 准备好部署

---

## 下一步行动

完成所有任务后：

1. **立即部署**：按照部署教程上传到 GitHub Pages
2. **测试验证**：在多个设备上测试游戏
3. **生成二维码**：使用分享指南生成二维码
4. **分享游戏**：发送给朋友试玩
5. **收集反馈**：根据反馈持续优化

---

**计划创建时间**：2026-02-28
**预计完成时间**：60-90 分钟
**难度等级**：中等
