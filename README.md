<div align="center">

# 📦 翻箱 FanBox

> *「AI 一下午帮你起十个项目，翻箱帮你找回它们。」*

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Release](https://img.shields.io/github/v/release/alchaincyf/fanbox?label=Release&color=blue)](https://github.com/alchaincyf/fanbox/releases/latest)
[![Platform](https://img.shields.io/badge/macOS-Apple%20Silicon-black?logo=apple)](https://github.com/alchaincyf/fanbox/releases/latest)
[![Runtime](https://img.shields.io/badge/Runtime-no--build-blueviolet)](#技术架构)

<br>

**vibe coding 的驾驶舱：左手翻文件，右手指挥 agent，中间看它改了什么。**

<br>

左边浏览、预览、轻改本机文件，右边内嵌真实终端跑 Claude Code 等 coding agent，<br>
agent 每改一个文件，对应卡片当场亮起——「找文件 → 跑 agent → 看它改」收进一个窗口。

<br>

[⬇ 下载 dmg](https://github.com/alchaincyf/fanbox/releases/latest) · [看截图](#三套皮肤) · [能做什么](#能做什么) · [安装](#安装) · [开源致谢](#建在巨人肩膀上)

</div>

---

<p align="center">
  <img src="assets/screenshot-volt.png" alt="翻箱 FanBox · 终端皮肤 · 左侧文件浏览 + 底部 README 预览 + 右侧内嵌终端" width="100%">
</p>

<p align="center"><sub>▲ 实拍：左边翻 fanbox 仓库本身，底部原地预览 README，右边内嵌终端跑 git。本 README 全部截图由 Playwright 驱动 app 实机拍摄，无修饰。</sub></p>

---

## 为什么要做翻箱

AI 帮你一个下午起十个项目，但它们散在各处、名字认不出、改了啥看不见。每天的真实流程是：Finder 里翻半天 → 切到 iTerm 启 agent → 再切浏览器看效果，三个窗口来回跳。

翻箱把这条链路收进一个窗口：**左边文件 × 右边/下边终端 × 原地预览**，一个有机整体。它不跟 Finder 拼文件操作，不跟 VS Code 拼编辑，专注「找回 + 预览 + 轻改 + 指挥 agent」这一条链路做到顺手。

不做云、不做远程、不做账号。本地、零配置、运行时零依赖。

## 三套皮肤

界面在 [huashu-design](https://github.com/alchaincyf/huashu-design) 辅助下完成设计，三套皮肤不是换个主题色——配色、字体、图标、代码高亮、终端 ANSI 主题整体随之变化：

| | |
|---|---|
| <img src="assets/screenshot-volt.png" alt="终端皮肤"> | **终端** · Volt 荧光绿 × 炭黑 × 等宽字，工业仪器面板感（默认） |
| <img src="assets/screenshot-archive.png" alt="档案皮肤"> | **档案** · 奶油纸 × 赤陶橙 × 衬线，温暖纸感档案馆 |
| <img src="assets/screenshot-index.png" alt="索引皮肤"> | **索引** · 黑白 × 信号红/绿 × 巨号字，编辑式索引日报 |

## 能做什么

### 文件 · 找回与预览

- **⌘K 全局模糊搜索**——记得名字片段就行；`⌘↵` 用编辑器整包打开项目；`内容:关键词` 切全文搜索。
- **强色实体图标**——每种文件「长得像它自己」：PDF 红、JS 黄、Markdown 蓝，照片视频按真实比例呈现，扫一眼就认出来。
- **原地预览**——Markdown 渲染、HTML 实时成品、代码语法高亮、图片/视频/PDF 内嵌（HEIC 直接显示）。
- **缩略图加速**——大文件夹滚动和点击都在 0.1 秒内。
- **项目徽章**——文件夹卡片标 node / web / py / rs / go 徽章，一下午起的十个项目一眼认出类型。

### 看 agent 改了什么

- **活的仪表盘**——agent 每写一个文件，那张卡片当场荡开涟漪、按改动频率发光呼吸，agent 写到哪光走到哪。
- **会话回放**——像刷视频一样拖时间轴，重现这段时间 agent 一步步改了哪些文件。
- **变更收件箱**——跨多个项目汇总本会话所有被改动的文件，多项目并行跑 agent 不再各看各的。
- **Git 改动 diff**——Monaco 只读 DiffEditor 并排展示 HEAD vs 当前工作区，看清 agent 到底改了哪几行。

### 终端 · 指挥 agent

- **真实内嵌终端**——node-pty + xterm.js（WebGL 渲染），跑 Claude Code / vim / htop 不花屏，中文宽字符正确。
- **拖文件进终端**——从文件列表拖文件/文件夹进终端，自动插入路径喂给 agent 当上下文。
- **路径可点击**——终端里出现的文件路径直接点击在翻箱打开；带空格的 macOS 截屏名、中文文件名、折行的长路径都能识别（空格边界由文件系统 stat 验证，不靠猜）。
- **选中即甩给终端**——预览里选一段文字，一键以「文件出处 + 围栏」格式发进终端（bracketed paste 包裹，不会被逐行误执行）。
- **态势感知**——标签圆点显示 agent 运行/空闲/退出；agent 把球踢回给你时终端边缘呼吸提示「轮到你」，长任务完成发系统通知。

### 编辑 · 所见即所得

- **Markdown**——Milkdown Crepe 提供 Notion 式所见即所得，打开就是编辑态，停笔 0.8 秒自动保存。
- **代码/JSON**——Monaco 编辑器（VS Code 同款内核），随皮肤切换主题。
- **图片标注**——画笔/箭头/文字/打码、格式转换、压缩、调分辨率，覆盖原图前有确认。
- **未保存守卫**——三种编辑器统一拦截未保存退出，Esc 旁路也堵死。

## 安装

### 桌面版（推荐）

从 [**Releases**](https://github.com/alchaincyf/fanbox/releases/latest) 下载最新 `.dmg`，拖进「应用程序」即可。Apple Silicon (arm64) 原生。

> 已用 Apple Development 证书签名 + hardened runtime。首次打开若提示「未验证的开发者」：**右键 → 打开 → 确认**即可。
>
> 应用内置**更新提醒**：检测到 GitHub 上有新 Release 时，右下角会弹一条提示引导下载，不强更、可对单个版本「不再提醒」。

### 网页版（不打包，直接跑）

```bash
node server.js
```

浏览器打开 `http://localhost:4567`。零依赖、零 build，clone 下来就能跑。网页版只有文件浏览/搜索/预览（内嵌终端和编辑器靠 Electron 提供）。

### 开发模式

```bash
npm install
npm run app          # electron . 启动完整桌面版
npm run dist         # 打包签名 .dmg（产物在 dist/，不入 git，统一走 Releases 分发）
```

> 打包遇到 Electron 下载被墙：`ELECTRON_MIRROR="https://registry.npmmirror.com/-/binary/electron/" npm run dist`

## 快捷键

| 操作 | 键 | 操作 | 键 |
|---|---|---|---|
| 全局搜索 | `⌘K` | 用编辑器打开 | `⌘↵` |
| 折叠侧栏 | `⌘B` | 后退 | `⌘[` |
| 当前目录筛选 | `/` | 打开/预览 | `↵` |
| 结果上下选择 | `↑` `↓` | 关闭 | `Esc` |

## 隐私与安全

- 后端只在本机回环地址监听 + 校验 Host 头（挡 DNS rebinding），**数据不出本机**。
- 全部前端资源（含渲染器、字体）本地内置，运行时无任何外网请求，**离线完全可用**。
- HTML 预览在隔离 origin 的沙箱 iframe 里渲染，预览不可信网页也碰不到终端能力。
- 配置写入走串行化读-改-写 + 原子写（temp + fsync + rename），不丢数据、不留半截 JSON。
- 删除走系统废纸篓（可恢复）；缩略图缓存按最旧优先自动裁剪，上限 400MB。

## 设计与验收

界面设计在 **[huashu-design](https://github.com/alchaincyf/huashu-design)** 辅助下完成——三套皮肤的方向探索、组件质感、反 AI slop 审查都出自它的工作流。图标是档案暖色陶土箱体 + 米纸 squircle，从 SVG 一路生成到 icns。

每个开发阶段由 **5 个独立 subagent** 扮演不同角色（重度 vibe coder / 原生审美设计师 / 零文档新用户 / 终端十年老兵 / 破坏性质量官），审「成品 + 真机截图 + 代码」打分，**全部 ≥90 分且无红线才算达标**。评分标准见 `docs/05-验收角色与评分标准.md`。

## 建在巨人肩膀上

翻箱的核心能力来自这些出色的开源项目，给足 credit：

| 项目 | 用在哪 | License |
|---|---|---|
| [Electron](https://www.electronjs.org/) | 桌面壳，让零依赖 Node 后端长出真实终端和原生能力 | MIT |
| [node-pty](https://github.com/microsoft/node-pty) | 伪终端，内嵌终端的「真 shell」来源 | MIT |
| [xterm.js](https://xtermjs.org/) | 终端渲染（含 [addon-webgl](https://github.com/xtermjs/xterm.js) GPU 加速、addon-fit 自适应、addon-unicode11 CJK 宽字符） | MIT |
| [Monaco Editor](https://microsoft.github.io/monaco-editor/) | 代码/JSON 编辑与 Git diff 视图，VS Code 同款内核 | MIT |
| [Milkdown](https://milkdown.dev/)（Crepe） | Markdown 所见即所得编辑 | MIT |
| [marked](https://marked.js.org/) | Markdown 预览渲染 | MIT |
| [highlight.js](https://highlightjs.org/) | 代码语法高亮 | BSD-3-Clause |
| [esbuild](https://esbuild.github.io/) | 把 Milkdown 打成单文件本地 vendor，运行时保持 no-build | MIT |
| [electron-builder](https://www.electron.build/) | 打包签名 dmg | MIT |
| [Playwright](https://playwright.dev/) | 驱动 Electron 实拍本 README 截图 + UI 验证 | Apache-2.0 |

所有前端依赖都 vendor 到本地（`public/vendor/`），这是「离线完全可用」的底气，也意味着上面每个项目的代码真实地跑在你机器上。谢谢它们。

## 技术架构

| 层 | 用什么 |
|---|---|
| 后端 | 零依赖 Node.js `server.js`（文件 API + 静态服务 + 缩略图） |
| 桌面壳 | Electron 33 + node-pty（asarUnpack 原生模块） |
| 终端 | xterm.js + WebGL + unicode11 |
| 编辑器 | Monaco（代码）+ Milkdown Crepe（Markdown） |
| 打包 | electron-builder → 签名 arm64 .dmg |

<details>
<summary>项目结构</summary>

```
fanbox/
├── server.js               # 零依赖 Node 后端（文件 API + 缩略图 + 静态服务）
├── electron/
│   ├── main.js             # 主进程（窗口/pty/剪贴板/fs.watch/菜单）
│   └── preload.js          # 暴露 fanboxPty / fanboxFs / fanboxClipboard
├── public/
│   ├── index.html
│   ├── style.css
│   ├── app.js              # 前端单页应用
│   └── vendor/             # xterm / monaco / milkdown 本地资源
├── src-vendor/             # esbuild 入口，产出 public/vendor/milkdown
├── build/                  # 图标 + entitlements
├── docs/                   # 概念/PRD/路线图/验收标准
└── experiments/            # 实验脚本（含 README 截图脚本）
```

</details>

## 关于作者

**花叔 Huashu**——AI Native Coder，独立开发者。代表作：小猫补光灯（App Store 付费榜 Top1）。

| 平台 | 链接 |
|------|------|
| 🌐 官网 | [bookai.top](https://bookai.top) · [huasheng.ai](https://www.huasheng.ai) |
| 𝕏 Twitter | [@AlchainHust](https://x.com/AlchainHust) |
| 📺 B站 | [花叔](https://space.bilibili.com/14097567) |
| 📕 小红书 | [花叔](https://www.xiaohongshu.com/user/profile/5abc6f17e8ac2b109179dfdf) |
| 💬 公众号 | 微信搜「花叔」 |

更多 AI 造物：[女娲.skill](https://github.com/alchaincyf/nuwa-skill)（蒸馏任何人的思维方式）· [huashu-design](https://github.com/alchaincyf/huashu-design)（一句话拿回一份能交付的设计）

---

<div align="center">

**Finder** 帮你管理文件。<br>
**IDE** 帮你写代码。<br>
**翻箱** 帮你看清 AI 在你机器上干了什么。<br><br>

MIT License © [花叔 Huashu](https://github.com/alchaincyf)

</div>
