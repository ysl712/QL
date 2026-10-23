# AI 助手接口预留

## 当前接入方式

页面启动后提供 `window.PiggyLedger`（同一对象也可通过 `window.PiggyLedgerAPI` 访问）。AI 助手如果运行在这个网页内，或通过用户授权的浏览器扩展/本机桥接器控制页面，可以调用这些方法：

```js
PiggyLedgerAPI.getAllTransactions()
PiggyLedgerAPI.getMonthlySummary('2026-10')
PiggyLedgerAPI.getCategoryStats('2026-10')
PiggyLedgerAPI.getCategories()
PiggyLedgerAPI.getBudget()
PiggyLedgerAPI.addTransaction({
  type: 'expense', amount: 28.5, category: '餐饮',
  date: '2026-10-23', note: '午饭'
})
PiggyLedgerAPI.updateTransaction('txn-id', { note: '和同事午饭' })
PiggyLedgerAPI.deleteTransaction('txn-id')
PiggyLedgerAPI.setBudget(3000)
```

`addTransaction` 会校验收支类型、正数金额、分类和日期；更新/新增会写入本机 `localStorage`。AI 写入账单前应向用户确认，删除前应再次确认。日期格式为 `YYYY-MM-DD`，金额单位为元。汇总返回 `{ month, income, expense, balance, count }`，分类统计返回 `{ 分类名: 金额 }`。

头像也可由同页面助手读取/设置：`getAvatar()` 返回缩小后的图片 data URL，`setAvatar(dataUrl)` 设置头像，传 `null` 恢复小猪头像。

## 后续服务端 REST 接口建议

这是静态网站，不会自行提供可被远程 AI 调用的 HTTP API。若 AI 助手独立运行，需要增加带用户授权和鉴权的后端/MCP 适配器。建议保持以下 REST 合约与当前浏览器 API 对应：

| 方法 | 路径 | 用途 |
| --- | --- | --- |
| GET | `/api/v1/transactions` | 查询账单，可用 `month`、`type`、`category` 筛选 |
| POST | `/api/v1/transactions` | 新增账单 |
| PATCH | `/api/v1/transactions/{id}` | 更新账单字段 |
| DELETE | `/api/v1/transactions/{id}` | 删除账单 |
| GET | `/api/v1/summary?month=YYYY-MM` | 查询月度收入、支出、结余 |
| GET | `/api/v1/category-stats?month=YYYY-MM` | 查询支出分类统计 |
| GET / PUT | `/api/v1/budget` | 读取或更新月预算 |

服务端应在写入时执行同样的数据校验，并通过登录会话/API token 或本机 MCP 权限限制访问。不要把账单或访问密钥发送给未经授权的第三方。
