# 发布到 DSH 插件社区（awesome-dsh-plugin / dsh-market）

本文档基于 2026-10-17 的官方贡献指南整理：
<https://github.com/awesome-dsh-plugin/awesome-dsh-plugin/blob/main/contributing.md>

## 结论（一句话）

DSH 本体没有内置的插件商店；社区市场（dshmarket）的插件列表来自 GitHub 仓库
[`awesome-dsh-plugin/awesome-dsh-plugin`](https://github.com/awesome-dsh-plugin/awesome-dsh-plugin)。
**上架 = 把你的插件放到一个公开 GitHub 仓库，然后向该仓库提一个 PR，新增一个 YAML 条目文件。**

## 上架前置条件（官方要求）

1. 公开 GitHub 仓库，仓库里有真实可用的插件代码。
2. `package.json` 声明 **`dsh.bundle`**（只有 `dsh.client` 不够，会被拒）：
   ```jsonc
   "dsh": {
     "bundle": { "patch": "./cordis.patch.yml" },
     "client": { "platform": "web" }
   }
   ```
   并在仓库根放置 `cordis.patch.yml`。
3. 仓库创建满 **1 天**（CI 自动检查，PR 前几分钟才建仓库会被拒）。
4. 给仓库加 `dsh-plugin` topic。
5. 描述必须属实、不带营销词，评审会对着代码核对描述里的每个声明。
6. （推荐）发布 npm 包：`package.json` 的 `repository` 必须指回本列表收录的仓库。
   不发 npm 也可以；此时建议在 GitHub Release 上传预构建 `.tgz` 并用条目里的
   `tarball:` 字段指向它（必须是 GitHub Release 托管的 https `.tgz`）。
7. （可选，推荐）在仓库 `package.json` 旁放 `screenshots.json`，列 1–8 张仓库内图片路径，
   市场详情页会像 App Store 一样展示。**注意：截图会公开，不要用包含真实工作内容、人名、
   项目名的界面截图**——需要时先用演示数据（如「示例项目 A」「日常习惯」）另拍，或干脆不放。

## 具体步骤

### 第 1 步：把本目录变成独立仓库并推到 GitHub

### 方式 A：全程网页操作（不用 git，推荐不写代码的人）

1. 打开 <https://github.com/new>（或右上角 `+` → **New repository**）。
   > 注意是 **New repository**，不是 "Projects"。
2. 填写：
   - **Repository name**：`dsh-task-board`
   - 勾 **Public**
   - 不要勾 "Add a README file"（我们上传的文件夹里已经有一个）
3. 点 **Create repository**。
4. 在跳转后的空仓库页面点 **uploading an existing file** 链接。
5. 打开本机文件夹 `publish/dsh-task-board`，**全选里面的内容**（`Ctrl+A`），拖进浏览器上传区
   （Chrome / Edge 支持拖入文件夹并保留子目录；`locale`、`assets` 两个子文件夹要一起拖进去）。
6. 底部 **Commit changes**。
7. 回到仓库首页 → 右上 **About** 旁的齿轮 → **Topics** 输入 `dsh-plugin` → 保存。

需要上传的文件/文件夹清单：

```
package.json  index.js  client.js  cordis.patch.yml  icon.svg
README.md  LICENSE  PUBLISHING.md  screenshots.json  locale/  assets/
```

> `PUBLISHING.md` 是给你自己看的说明，可以一并上传（不影响收录），也可以删掉。

### 方式 B：命令行（会 git 的话）

```sh
cd publish/dsh-task-board
git init
git add .
git commit -m "feat: dsh-task-board 1.0.0"
# 先在 GitHub 建一个空仓库 GinoXiang/dsh-task-board，然后：
git remote add origin https://github.com/GinoXiang/dsh-task-board.git
git branch -M main
git push -u origin main
```

推送前先把占位符替换掉（见下方“需要替换的占位符”）。

到 GitHub 仓库页面 → About（齿轮）→ Topics，添加 `dsh-plugin`。

### 第 2 步（可选，推荐）：发 npm 或用 GitHub Release 挂安装包

- **发 npm**（有 npm 账号时，市场能显示下载量）：
  ```sh
  cd publish/dsh-task-board
  npm login
  npm publish --access public
  ```
- **不发 npm**（更简单，完全够用）：把 `publish/release/dsh-task-board-1.0.0.tgz`
  作为附件挂到 GitHub Release：
  仓库首页右侧 **Releases** → **Draft a new release** → Tag 填 `v1.0.0` →
  标题 `v1.0.0` → 把 tgz 拖进附件区 → **Publish release**。
  awesome 条目里的 `tarball:` 用下面的固定标签地址（已写进模板）：
  ```
  https://github.com/GinoXiang/dsh-task-board/releases/download/v1.0.0/dsh-task-board-1.0.0.tgz
  ```

注意：`dsh-task-board` 这个包名目前在 npm 上未被占用（2026-10-18 查询返回 404）；
若发布时已被占用，改成带 scope 的 `@<npm用户名>/dsh-task-board`，同时同步修改：
- `package.json` 的 `name`
- `cordis.patch.yml` 里 `name:`
- `client.js` 顶部的 `window.__ModuleLoader__.load({ id: '...' })` 与 `return { name: '...' }`
- 安装命令与条目描述里的包名

### 第 3 步：等仓库满 1 天

CI 会自动检查仓库年龄，不满 1 天会被拒；没有其他历史门槛，等一天后重新提交即可。

### 第 4 步：提 PR

在 `awesome-dsh-plugin/awesome-dsh-plugin` 仓库（默认分支 `main`）新增**一个文件**：

```
data/plugins/GinoXiang__dsh-task-board.yml
```

内容用 `awesome-entry/owner__dsh-task-board.yml` 模板，替换 `GinoXiang`。
文件名里的 `owner` 必须与 GitHub 用户名一致（大小写按仓库规则）。

条目格式（官方）：
```yaml
url: https://github.com/GinoXiang/dsh-task-board
name: GinoXiang/dsh-task-board
category: ui
description:
  en: '...'   # 必填；含 ": " 时必须加引号
  zh: '...'   # 可选，维护者会补
# 可选（推荐，若没发 npm）：
# tarball: https://github.com/GinoXiang/dsh-task-board/releases/latest/download/dsh-task-board.tgz
```

- 一个 PR 最多 3 条（我们只提交 1 条）。
- 不要手工改仓库根的两个 README（它们由脚本从 `data/plugins/*.yml` 生成）。
- 分类选 `ui`（插件页面属于 Web UI 增强）；分类选得不准维护者会直接改，不会打回。

### 第 5 步：等 CI + 维护者审核

CI 依次检查：条目数量 → `dsh.bundle` → 仓库年龄 → awesome-lint 与站点构建。
CI 通过是前置条件，维护者还会阅读仓库源码核对你描述里的内容。
反馈会以 PR 评论给出；在同一分支推送修复即可，无需重开 PR。
合并后官网与 dsh-market 市场通常一天内自动收录。

## 需要替换的占位符

| 占位符 | 位置 | 说明 |
|---|---|---|
| `GinoXiang` | `package.json`（homepage/repository/bugs）、`README.md`、`awesome-entry` | GitHub 用户名 |
| `GinoXiang` | `package.json` 的 `author`、`LICENSE` 的版权行 | 作者署名 |
| `owner` | `awesome-entry/owner__dsh-task-board.yml` 文件名 | 改成 GitHub 用户名 |

## 另：DSH 原生 Discover 的“自定义索引”不是这个流程

DSH 自带的 Discover 来自用户自己配置的市场索引 URL（JSON 格式，见
`@deepseek-ai/dsh-plugin-market-index` 文档），插件作者不需要发布索引才能上架；
上面第 4 步收录进 awesome-dsh-plugin 后，dsh-market 市场即可搜索/一键安装。

## 本地开发版与发布版不要并存

当前机器上安装的是本地开发版 `@local/dsh-task-board`（profile 链接目录）。
以后从 npm / GitHub 安装发布版 `dsh-task-board` 时，两份插件都会注册同一个侧边栏
图标和同一个 `main` 面板 key（`task-board`），会互相覆盖、行为难以预测。
切换前先在插件管理里卸载/停用本地开发版，只保留一份。

## PR 文案模板

标题：

```
Add dsh-task-board (personal task board for the Web GUI)
```

正文：

```
## What

Adds `dsh-task-board`, a personal task board for the DSH Web GUI:

- two-month calendar with colored per-task timeline bars (drag to move / resize,
  elapsed days shown as a hazy hatched band)
- daily checklist
- weekly seven-column view with completion checkboxes, and a 24-hour day
  timeline with draggable / resizable blocks
- AI task extraction from natural language, with a local rule-based fallback
- data stored as one JSON file under `DSH_HOME/task-board/data.json`

## Checklist

- [x] `dsh.bundle` manifest (`dsh.bundle.patch` + `cordis.patch.yml`)
- [x] real, working code (no build step, no dependencies)
- [x] repo has the `dsh-plugin` topic
- [x] description matches the implementation
- [x] screenshots.json with 4 screenshots, task names redacted
- [x] npm package published with `repository` pointing back to this repo
```

