# AI 生成可运行的消除游戏（HTML5 / Phaser 3）

> **EN:** An AI-generated, playable HTML5 game built with **Phaser 3**. This repository ships a ready-to-run build (`dist/`) and optional tooling conventions for spec-driven, repeatable game generation.

本仓库用于展示/交付一套 **AI 生成的可运行游戏代码（或构建产物）**。你可以直接本地启动试玩，或部署到静态站点（GitHub Pages / Cloudflare Pages / Vercel / NetGameX CDN）。

---

## 你会得到什么

- ✅ **可运行版本（`dist/`）**：开箱即玩（无需编译）
- ✅ **完整资源**：UI 图片、音效（mp3）、模型/贴图（fbx/png）
- ✅ **移动端友好**：全屏、禁用页面滚动、触控优先
- ✅ **可部署**：任何静态服务器都能跑

> 说明：你上传的构建包中 `dist/index.html` 的标题是 **“Draw The Defense - Rebuild”**。如果你希望仓库在 GitHub 上显示为“消除游戏”，可直接修改 `dist/index.html` 的 `<title>`。

---

## 快速开始（直接运行 dist）

### 方式 A：Node 静态服务器（推荐）
```bash
# 进入 dist 所在目录（如果你的仓库根目录就有 dist）
cd dist

# 方式 1：http-server
npx http-server -p 5173

# 方式 2：serve
npx serve -s . -l 5173
```

浏览器打开：`http://localhost:5173`

### 方式 B：Python（最省事）
```bash
cd dist
python3 -m http.server 5173
```

浏览器打开：`http://localhost:5173`

> ⚠️ 不建议双击打开 `dist/index.html`（file://），有些浏览器会限制资源加载。请用本地静态服务器。

---

## 玩法与交互（以实际游戏为准）

由于当前包为 **生产构建（bundle）**，玩法逻辑被打包到 `dist/assets/index-*.js` 中。  
为了让 README 在仓库层面可读、可维护，建议你在仓库补充一个 **Game Spec**（见下方模板），把玩法从代码里“抽出来”。

根据构建包携带的 UI 资源，本游戏包含或可能包含以下交互元素：

- `btnRestart`：重新开始
- `btnSound`：音效开关
- `btnNextLevel`：下一关/继续
- `btnAttack / btnDefense / btnInk`：技能/模式按钮（具体效果以游戏内为准）
- `coin / btnRewardCoin`：奖励/货币显示与领取

音效资源（示例）：
- `button.mp3`（按钮）
- `shoot.mp3 / hit.mp3`（攻击/命中）
- `win.mp3 / fail.mp3`（胜负）
- `upgrade.mp3`（升级）
- `music.mp3`（背景音乐）

---

## 目录结构（构建包）

```
dist/
├─ index.html                         # 入口
└─ assets/
   ├─ index-*.js                      # 打包后的游戏逻辑（Phaser 3 bundle）
   ├─ index-*.css                     # 全屏/触控样式
   ├─ Images/                         # UI、粒子等图片资源
   ├─ Sound/                          # 音效/音乐
   └─ Models/                         # 模型与贴图（fbx/png）
```

---

## 部署（Deploy）

### 1) GitHub Pages（最简单）
如果你的仓库只包含 `dist/`（纯静态）：

**方案 A：把 `dist/` 内容放到仓库根目录**  
- 让 `index.html` 位于仓库根目录
- Settings → Pages → Deploy from branch → `/ (root)`

**方案 B：使用 `docs/` 目录**（GitHub Pages 原生支持）
- 把 `dist/` 重命名为 `docs/`
- Settings → Pages → Deploy from branch → `/docs`

### 2) Cloudflare Pages / Vercel / Netlify
- Build command：空（如果只有 dist）
- Output directory：`dist`

---

## 如果你有“源码仓库”（推荐做法）

当前上传的是构建产物。如果你希望“AI 生成可运行代码仓库”真正可持续迭代，建议你的 repo 至少包含：

- `src/`：Phaser 3 源码（Scene、Systems、Config）
- `public/assets/`：原始资源（不要只放打包后的 hash 文件）
- `package.json`：开发/构建脚本
- `specs/`：玩法规范（Game Spec）
- `prompts/`：AI 分步提示词（Task 1/2/3…）

一个推荐结构如下（可按需调整）：

```
.
├─ src/
│  ├─ main.ts
│  ├─ game/
│  │  ├─ config.ts
│  │  ├─ scenes/
│  │  └─ systems/
│  └─ ui/
├─ public/
│  └─ assets/
├─ specs/
│  └─ game-spec.md
├─ prompts/
│  ├─ task1-scaffold.md
│  ├─ task2-core-loop.md
│  └─ task3-powerups.md
├─ dist/                               # build 输出（可选提交）
└─ package.json
```

---

 

```md
# Game Spec — 消除游戏（Tap-to-Remove）

## 目标
- 单局时长：30 秒
- 核心乐趣：快速点击消除 + 连击反馈 + 道具策略

## 棋盘
- 行列：8x8（可配置）
- 颜色/类型：5 种（可配置）
- 生成规则：避免开局无可消除

## 规则
- 点击一组相邻同色（>=2）触发消除
- 下落补位：重力下落 + 顶部补生成
- 计分：基础分 + 连击倍数

## 道具
- Bomb：消除 3x3
- Rainbow：清除一种颜色
- 生成概率：与连击/进度挂钩（可配置）

## 胜负
- 30 秒结束结算
- 目标分/星级（可配置）

## 反馈
- 消除粒子：sparkle / ring / streak
- 音效：click / combo / win / fail
- 屏幕抖动：道具触发时轻微

## 可配置参数（Config）
- gridSize, colors, minGroup, roundTime, spawnSeed, powerupChances...
```

---

## 常见问题（Troubleshooting）

**1) 为什么本地打开是黑屏？**  
- 多数是因为用 `file://` 打开导致资源加载受限。请用 `http-server / serve / python http.server`。

**2) 手机上不能滚动/缩放？**  
- 这是刻意设计：`touch-action:none` + `user-scalable=no`，避免误触带来的页面缩放。

**3) 资源路径 404？**  
- 确认你是从 `dist/` 目录启动静态服务器，并且 `index.html` 中 `./assets/...` 相对路径正确。

 

## License / 版权与素材

- **代码**：使用 MIT 或自定义商业许可
 
---

## Contact

- NetGameX: https://netgamex.com    test，link：https://gameres.netgamex.com/10gameh5/dist/index.html
- Repo owner: NetGameX Studio 
