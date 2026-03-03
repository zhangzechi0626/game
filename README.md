# 分类倒水游戏

<div align="center">

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![PWA](https://img.shields.io/badge/PWA-5A0FC8?style=for-the-badge&logo=pwa&logoColor=white)

一款经典的分类倒水益智游戏，专为移动端优化，支持离线游玩

[在线演示](https://你的用户名.github.io/water-sort-puzzle/) · [查看文档](#文档导航) · [报告问题](../../issues)

</div>

---

## 项目简介

分类倒水游戏是一款简单而富有挑战性的益智游戏。玩家需要将不同颜色的水倒入试管中，使每个试管中的水颜色一致才能过关。游戏规则简单易懂，但随着关卡难度的提升，需要更多的策略思考。

本项目已经过移动端优化，采用渐进式 Web 应用（PWA）技术，支持添加到手机桌面，提供接近原生应用的体验。

## 功能特性

### 游戏特性
- 5 个精心设计的关卡，难度循序渐进
- 流畅的倒水动画效果
- 直观的操作提示和反馈
- 胜利弹窗与关卡切换
- 随时重新开始当前关卡

### 技术特性
- 完全响应式设计，适配各种屏幕尺寸
- PWA 支持，可添加到手机桌面
- 离线可用，玩过一次后无需联网
- 触摸优化，针对移动端交互体验
- 优雅的加载动画
- 支持横屏和竖屏模式
- 适配 iPhone 刘海屏和 Android 全面屏

## 在线演示

访问以下链接即可在线游玩：

```
https://你的用户名.github.io/water-sort-puzzle/
```

> 请将上述链接中的 `你的用户名` 替换为你的实际 GitHub 用户名。如果你还没有部署，请查看 [部署教程](./docs/guides/部署教程.md)。

## 快速开始

### 方式一：在线游玩
直接访问已部署的游戏链接，无需下载安装。

### 方式二：本地部署

1. **克隆或下载项目**
   ```bash
   git clone https://github.com/你的用户名/water-sort-puzzle.git
   cd water-sort-puzzle
   ```

2. **打开游戏**
   - 双击 `index.html` 文件
   - 或使用浏览器打开 `index.html`

3. **开始游玩**
   - 选择关卡
   - 点击试管进行倒水操作
   - 完成关卡挑战

## 部署指南

想要将游戏部署到互联网上，让朋友也能玩？我们提供了完整的部署指导文档。

### 快速部署步骤

1. 注册 GitHub 账号（如果还没有）
2. 创建新仓库并上传游戏文件
3. 启用 GitHub Pages
4. 分享游戏链接给朋友

### 详细文档

由于部署过程包含多个步骤，我们准备了详细的分步指导文档：

- [GitHub注册指导](./docs/guides/GitHub注册指导.md) - 从零开始注册 GitHub 账号
- [部署教程](./docs/guides/部署教程.md) - 将游戏部署到 GitHub Pages
- [分享指南](./docs/guides/分享指南.md) - 如何分享游戏给朋友

## 文档导航

本项目提供完整的使用文档，适合不同需求的用户：

| 文档名称 | 适用人群 | 说明 |
|---------|---------|------|
| [GitHub注册指导](./docs/guides/GitHub注册指导.md) | 没有 GitHub 账号的新手 | 详细的注册流程，包含截图和常见问题 |
| [部署教程](./docs/guides/部署教程.md) | 想要部署游戏的用户 | 两种部署方式（网页上传/命令行），包含故障排除 |
| [分享指南](./docs/guides/分享指南.md) | 想要分享游戏的用户 | 多种分享方式，包含二维码生成、短链接等 |

## 技术栈

### 前端技术
- **HTML5** - 语义化标签，移动端 meta 配置
- **CSS3** - 现代化样式技术
  - Flexbox 布局
  - CSS 动画和过渡效果
  - 媒体查询（响应式设计）
  - CSS 变量（颜色系统）
- **Vanilla JavaScript** - 纯 JavaScript 实现，无框架依赖
  - ES6+ 语法
  - 事件驱动架构
  - 状态管理

### PWA 技术
- **Web App Manifest** - 应用配置清单
- **Service Worker Ready** - 支持离线功能（需要配置）
- **移动端优化**
  - 触摸事件优化
  - 防止双击缩放
  - 防止双指缩放
  - Safe Area 支持（刘海屏适配）

### 主要技术特性
- 响应式设计（适配 320px - 1920px 屏幕）
- 触摸优化（防误触、快速响应）
- 性能优化（GPU 加速、will-change）
- 渐进式增强（基础功能降级支持）

## 浏览器支持

### 桌面浏览器
| 浏览器 | 最低版本 | 说明 |
|-------|---------|------|
| Chrome | 60+ | 推荐使用，体验最佳 |
| Edge | 79+ | 推荐使用 |
| Firefox | 60+ | 完全支持 |
| Safari | 12+ | 完全支持 |

### 移动浏览器
| 浏览器 | 最低版本 | 说明 |
|-------|---------|------|
| iOS Safari | 12+ | 推荐使用，体验最佳 |
| Chrome (Android) | 60+ | 推荐使用 |
| 微信内置浏览器 | - | 完全支持 |
| QQ 浏览器 | - | 完全支持 |
| 华为/小米/OPPO 等系统浏览器 | - | 完全支持 |

### 屏幕尺寸支持
- 超小屏手机：< 375px（iPhone SE）
- 小屏手机：375px - 414px（iPhone 6/7/8）
- 大屏手机：414px - 768px（iPhone Plus、Android 手机）
- 平板设备：768px - 1024px
- 桌面设备：> 1024px

## 项目结构

```
water-sort-puzzle/
├── index.html              # 主游戏文件（包含 HTML、CSS、JavaScript）
├── manifest.json           # PWA 配置文件
├── mega-background.png     # 游戏背景图片
├── icon-192.png           # PWA 图标（192x192）
├── icon-512.png           # PWA 图标（512x512）
├── README.md              # 项目说明文档（本文件）
├── ICONS-TODO.md          # 图标制作待办事项
└── docs/                  # 文档目录
    └── guides/            # 用户指南
        ├── GitHub注册指导.md
        ├── 部署教程.md
        └── 分享指南.md
```

### 主要文件说明

- **index.html** - 游戏主文件，包含所有逻辑、样式和交互
- **manifest.json** - PWA 配置，定义应用名称、图标、主题色等
- **mega-background.png** - 游戏背景图片，建议大小控制在 500KB 以内
- **icon-192.png / icon-512.png** - PWA 应用图标（需要自行创建）

## 游戏规则

### 基本规则
1. 点击一个试管选中它
2. 再点击另一个试管，将水倒入
3. 只能将水倒入空试管，或倒入顶部颜色相同的试管
4. 每个试管最多容纳 4 层水
5. 当所有试管都只包含一种颜色且装满时，即可过关

### 操作技巧
- 使用空试管作为临时存放位置
- 优先完成单一颜色的试管
- 仔细规划每一步，避免陷入死局
- 实在无法继续时，可以选择重新开始

### 关卡设计
- **关卡 1** - 3 种颜色，2 个空试管（入门）
- **关卡 2** - 3 种颜色，2 个空试管（进阶）
- **关卡 3** - 4 种颜色，2 个空试管（提高）
- **关卡 4** - 4 种颜色，2 个空试管（挑战）
- **关卡 5** - 5 种颜色，2 个空试管（困难）

## 更新日志

### v1.0.0 (2026-02-28)
- 初始版本发布
- 实现 5 个关卡
- 移动端优化
- PWA 支持
- 响应式设计
- 触摸优化
- 加载动画
- 完整的用户指导文档

## 贡献

欢迎贡献代码、报告问题或提出建议！

### 如何贡献
1. Fork 本项目
2. 创建特性分支（`git checkout -b feature/AmazingFeature`）
3. 提交更改（`git commit -m 'Add some AmazingFeature'`）
4. 推送到分支（`git push origin feature/AmazingFeature`）
5. 开启 Pull Request

### 改进建议
- 添加更多关卡
- 增加难度选择
- 添加撤销功能
- 添加步数统计
- 添加音效和背景音乐
- 添加排行榜
- 支持多语言

## 作者信息

**制作者：张泽池**

如果你喜欢这个项目，欢迎：
- 给项目点个 Star
- 分享给你的朋友
- 提出改进建议
- 贡献代码

## 许可证

本项目采用 MIT 许可证。详见 [LICENSE](LICENSE) 文件。

```
MIT License

Copyright (c) 2026 张泽池

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## 致谢

感谢以下技术和工具的支持：
- [GitHub Pages](https://pages.github.com/) - 免费的静态网站托管服务
- [PWA](https://web.dev/progressive-web-apps/) - 渐进式 Web 应用技术
- [Claude Code](https://claude.ai/) - AI 辅助开发工具

---

<div align="center">

**如果这个项目对你有帮助，请给它一个 Star！**

Made with ❤️ by 张泽池

[返回顶部](#分类倒水游戏)

</div>
