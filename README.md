# PiWork-Plugin-Browser

[English](README.en.md) | 简体中文

**PiWork 官方插件 · 浏览器自动化 · `com.pi.browser`**

把 PiWork 内嵌的浏览器交给 agent：打开网页、读取页面结构、点击、填表、上传文件、滚动、截图，也能按目标自动走完多步。浏览器面板里看到的就是 agent 正在操作的那个页面——它导航，你那边跟着变；你切到别的标签或自己点页面，agent 看到的也是同一个页面。登录态与宿主浏览器共用，不需要在插件里再登录一次。

## PiWork 中的浏览器自动化

![PiWork 里的浏览器面板](./assets/screenshot.png)

下面的演示里，一句话让 agent 打开网页、点击并汇报结果。

![浏览器自动化演示](./media/demo.gif)

## 能做什么

- **页面操作** —— 导航、点击、填表、下拉选择、悬停、滚动、上传本地文件；支持同源与跨域 iframe、拖拽上传和原生确认框。每一步都可以读回页面快照验证结果。
- **理解页面** —— 返回可交互元素快照（按钮 / 链接 / 输入框各带一个 ref），按文字模糊查找，必要时执行页面内 JS 或截图。
- **多标签与分屏** —— 打开、切换、关闭标签；分屏时每个页面各自独立，地址栏和历史跟着当前标签走。
- **历史与收藏** —— 每次导航自动记录历史（按网址去重，保留最近 500 条）；星标收藏常用站点，地址栏下拉直达；agent 也能查询，用户说「上次那个网站」时不用重打网址。
- **本站记忆** —— 收藏一个站点后，可以在地址栏下拉里给它写几句长期知识（这个站是干什么的、入口在哪、不要动什么）。agent 每次打开该站会先看到这些内容，并按「记忆不是实时页面」的规则使用。agent 在成功操作后学到的事实会先进「待确认」，标为未确认地注入；你点 ✓ 后转正，点 ✕ 丢弃，已确认的条目 agent 改不了、删不了。
- **失败的账本与可疑记忆** —— 收藏站点上的每次多步操作都记下结果；失败时能看到第几步、什么错误，以及当时注入了哪几条记忆。页面与记忆矛盾时，agent 只能报告（标为可疑、立即暂停提供），不能绕过改记忆；可疑条目由你核对后恢复或删除。每条记忆保留修订历史，可一键回滚；配方的手动修改也有同样的历史。
- **跨网站工作流** —— 把反复走的固定套路记成一份清单（哪一步、在哪个站、用哪条配方），例如「每周导出订单再导入表格」。agent 按清单逐步执行，登录和确认框仍由你完成；使用次数和上次失败都能看到。
- **记忆中心** —— 一个独立面板汇总所有收藏站的记忆、待确认、可疑、失败账本和工作流，支持搜索；看到的内容就是 agent 读到的内容。
- **采集配方** —— 把「打开哪个页面、取哪些字段」记下来：一键运行会真实打开页面、只返回你点名的数据（例如趋势榜的仓库名/简介/今日新增 star），还能进入前几条子页面取详情。不产生页面快照，不调用模型；连续失败会标为失效。
- **快捷配方** —— 在收藏站点上成功执行的一批操作会自动记成配方（步骤、目标、备选定位）。之后 agent 可以按配方直接执行，站点小改也能命中备选定位。
- **划词助手** —— 网页里选中文字后弹出「翻译 / 解释 / 朗读 / 收藏」和问题输入框；agent 的回答直接回显在框里，而不是只出现在对话中。
- **整页翻译** —— 工具栏「译」展开一行控制条：原文替换、双语对照、整页朗读、整页收藏。译文按「站点 + 原文哈希」缓存，重访同一页不重复调用模型。正文的**解释 / 分析**不走这一行里的模型调用，而是把正文交给对话里的 agent，答案就在对话里（可继续追问、可留存）。
- **26 个 agent 工具** —— 覆盖导航、读取、交互、标签页、历史、收藏、本站记忆、跨站工作流、采集配方、配方和展示。

## 在 PiWork 里安装

1. 打开 PiWork 桌面端。
2. 右侧栏 →「插件中心」→「浏览器自动化」→ 安装。
3. 首次安装会显示权限清单；确认后下载并校验（sha256），装好即出现在「打开插件」菜单里。

Web 端不加载插件；浏览器能力需要桌面端。

从源码开发：

```bash
git clone https://github.com/PiWorkAgent/PiWork-Plugin-Browser
cd PiWork-Plugin-Browser
npm install
npm run build        # 后端 → dist/main.mjs（SDK 会被打包进去）
```

在 PiWork 仓库里实时调试：

```bash
PI_DEV_PLUGINS=/path/to/PiWork-Plugin-Browser pnpm dev:desktop:plugins
```

面板是 `ui/` 下的静态页面（不是构建产物），后端入口由 `manifest.json` 的 `backend` 声明。

## 对话里的用法

| 你说 | 会发生什么 |
| --- | --- |
| 「打开 Hacker News，看看今天前十条」 | agent 导航、读取快照、整理结果 |
| 「在 GitHub 搜 X，把第一个仓库的安装步骤念给我听」 | 导航、搜索、点击、读取，再用宿主语音朗读 |
| 「帮我把这份表单填了，文件用 ~/Downloads/a.pdf」 | `browser_fill` 填表 + `browser_upload` 上传本地文件 |
| 「回到我上次看的那个页面」 | `browser_history_search` 找到并打开 |
| 「把这段英文翻译一下」 | 划词弹框 → agent 用 `browser_show` 把译文回显在框里 |
| 「保存这个页面，下次按这个流程再走一遍」 | 收藏站点后，成功的 `browser_act` 被记成配方 |

## 工具一览

| 分组 | 工具 |
| --- | --- |
| 导航 | `browser_navigate` |
| 标签页 | `browser_tab_list` · `browser_tab_open` · `browser_tab_switch` · `browser_tab_close` |
| 读取 | `browser_get_state` · `browser_find` · `browser_screenshot` · `browser_evaluate` · `browser_walk` |
| 交互 | `browser_click` · `browser_fill` · `browser_act` · `browser_hover` · `browser_select` · `browser_scroll` · `browser_upload` |
| 历史 / 收藏 / 配方 | `browser_history_search` · `browser_favorites_list` · `browser_recipes` · `browser_recipe_delete` |
| 展示 / 片段 | `browser_show` · `browser_clips_list` |

标准循环是 `browser_navigate → browser_get_state → browser_click / browser_fill → browser_get_state`。完整语义、选择器格式和边界条件见 [PLUGIN.md](./PLUGIN.md)（这份文件会随插件一起交给 agent）。

## 权限

| 权限 | 用途 |
| --- | --- |
| `browser` | 使用宿主内嵌浏览器引擎、标签页、页面会话与登录态 |
| `storage` | 插件私有存储：历史、收藏、配方、划词片段 |
| `voice` | 划词与整页朗读（宿主 TTS） |
| `ai` | 整页翻译（走用户设置的默认对话模型，会消耗 token） |

## 发布

1. 改 `manifest.json` 的 `version`
2. 提交并推送 tag `v<version>`（或 Actions → Release → Run workflow）

Release 固定包含两个资产：

- `com.pi.browser-<version>.zip` — 运行时负载（manifest、`dist/`、`ui/`、`assets/`、`PLUGIN.md`）
- `index.json` — 单条目市场索引，`url` 指向本 Release（绝对地址），带 `sha256`

[PiWork-Registry](https://github.com/PiWorkAgent/PiWork-Registry) 会定时把各插件的 `index.json` 合并成客户端读取的市场索引。

## 目录

| 路径 | 说明 |
| --- | --- |
| `manifest.json` | 插件清单：id、版本、`apiVersion`、权限、面板、工具 |
| `src/backend/` | 后端源码（打包成 `dist/main.mjs`） |
| `dist/` | 后端打包产物（已提交，便于本地直接跑） |
| `ui/` | 面板页面 |
| `assets/` | 运行时图标 |
| `media/` | README 演示素材（不进入发布包） |
| `scripts/release.mjs` | 打包 + 生成单条目索引 |

## 相关链接

- [PiWork](https://github.com/PiWorkAgent/PiWork) — 宿主应用
- [PiWork-Registry](https://github.com/PiWorkAgent/PiWork-Registry) — 插件市场索引
- [插件开发指南](https://github.com/PiWorkAgent/PiWork-Plugin-Docs/blob/main/docs/plugin-dev-guide.md)
- [插件 SDK `@weiqun789/plugin-sdk`](https://www.npmjs.com/package/@weiqun789/plugin-sdk)
- [PLUGIN.md](./PLUGIN.md) — 写给 agent 的使用说明
