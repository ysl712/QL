# 小猪记账

手机竖屏优先的静态记账网站。直接用浏览器打开 `index.html` 即可使用；也可以将整个文件夹部署到任意静态网站托管服务。账单与预算保存在浏览器 `localStorage`，不会上传。

## 已实现

- 首页月度收入、支出、结余、预算进度与最近账单
- 支出/收入记账，账单编辑与删除，按月份/类型/分类筛选
- 月度汇总、近六个月趋势、分类支出统计
- 本地 JSON 导入与导出
- 粉色小猪视觉、手机安全区适配与桌面添加图标所需的 Web App manifest
- 支持上传自定义头像，缩小后保存在本机，也可恢复默认小猪头像

## MCP 接入预留

页面加载后将 `window.PiggyLedger` 暴露为轻量数据操作层：

- `getAllTransactions()`
- `addTransaction(data)`
- `updateTransaction(id, data)`
- `deleteTransaction(id)`
- `getMonthlySummary(month)`
- `getCategoryStats(month)`
- `getCategories()`
- `getBudget()` / `setBudget(value)`
- `getAvatar()` / `setAvatar(dataUrl)`

同时提供别名 `window.PiggyLedgerAPI`。面向 AI 助手的调用示例、数据格式和后续 REST 路径草案见 [AI_API.md](AI_API.md)。

账单字段为 `id`、`type`（`income` / `expense`）、`amount`、`category`、`date`、`note`、`createdAt`、`updatedAt`。存储键为 `piggy-ledger-v1`，结构为 `{ version, transactions, budget }`。

这份交付预留了供 MCP 适配器或同页 AI 助手调用的数据层，并在“我的”页展示接入说明。静态网页本身不能启动 MCP 服务，也不能让远程 MCP host 直接读取浏览器隔离区。真正联通独立 AI 服务时需要一个用户授权的本机 MCP 服务/浏览器桥接器或鉴权后端，再将工具映射到上述方法；当前产品不包含服务端。用户也可以通过 JSON 导入/导出手动交换数据。

## 本地预览

双击 `index.html` 可直接使用核心功能。若要安装到手机桌面，请通过 HTTPS 静态托管打开网站后使用浏览器的“添加到主屏幕”。
