# PiWork-Plugin-Browser

PiWork 插件：浏览器自动化。插件 id `com.pi.browser`。

通过 [PiWork-Registry](https://github.com/PiWorkAgent/PiWork-Registry) 出现在 PiWork 的插件中心，安装/更新由客户端完成。

## 开发

```bash
npm install
npm run build        # 后端 → dist/main.mjs（SDK 会被打包进去）
```

在 PiWork 仓库里实时调试：

```bash
PI_DEV_PLUGINS=/path/to/PiWork-Plugin-Browser pnpm dev:desktop:plugins
```

面板是 `ui/` 下的静态页面（不是构建产物），后端入口由 `manifest.json` 的 `backend` 声明。

## 发布

1. 改 `manifest.json` 的 `version`
2. 提交并推送 tag `v<version>`（或 Actions → Release → Run workflow）

Release 固定包含两个资产：

- `com.pi.browser-<version>.zip` — 运行时负载（manifest、`dist/`、`ui/`、`assets/`、`PLUGIN.md`）
- `index.json` — 单条目市场索引，`url` 指向本 Release（绝对地址），带 `sha256`

registry 会定时把各插件的 `index.json` 合并成客户端读的市场索引。

## 目录

| 路径 | 说明 |
| --- | --- |
| `manifest.json` | 插件清单：id、版本、`apiVersion`、权限、面板、工具 |
| `src/backend/` | 后端源码（打包成 `dist/main.mjs`） |
| `dist/` | 后端打包产物（已提交，便于本地直接跑） |
| `ui/` | 面板页面 |
| `scripts/release.mjs` | 打包 + 生成单条目索引 |
