# AGENTS.md — scene-manage 管理端（题目01 电商订单交易系统）

> 本文件是管理端项目的最高约束。**任何 AI Agent 生成或修改代码前，必须先读取并遵守本文件；每次生成代码都要走一遍本文件约束。** 学生未明确同意，任何 Agent 不得修改本文件。

## 1. 项目定位

- 仓库：https://github.com/ovo133838-lang/scene-manage （分支 `main` 仅初始化，开发在 `dev/01`）
- 技术栈：vue-element-admin（Vue2 + Element UI），对接后端 scene 仓库（Gin）。
- 本题管理端承载三角色：运营 / 仓库·商家 / 客服，按 RBAC 隔离菜单与接口。

## 2. 整体框架结构（不得擅自修改）

基于 vue-element-admin 现有目录，**不得改名/删除其核心框架目录**（`src/router`、`src/store`、`src/permission.js`、`src/layout` 等）；业务代码只允许落在约定位置：

```text
vue-element-admin/
├── src/
│   ├── api/            # 后端接口封装（按模块新增 shop.js / product.js / order.js / refund.js / shipment.js / summary.js）
│   ├── views/
│   │   ├── product/    # 运营：商品管理、库存看板
│   │   ├── summary/    # 运营：交易汇总
│   │   ├── shipment/   # 仓库：可发货订单、已发货、发货数据
│   │   └── refund/     # 客服：退款审核、异常支付、退款重试
│   ├── router/         # 动态路由 + 按角色过滤菜单
│   └── store/          # 登录态、角色、权限
├── mock/               # 仅脚手架自带示例；业务联调改走真实后端，不依赖 mock
├── AGENTS.md
└── .env.development / .env.production   # 后端 baseURL 配置
```

约定：菜单可见性由后端返回的角色权限决定，前端仅做隐藏，**权限最终以后端接口 401/403 为准**。

## 3. 对接的后端接口（已固化，不得自行发明）

- 登录：`POST /api/login`（返回 JWT + 角色）
- 运营：`GET /api/admin/products`、`POST /api/admin/products/{id}/shelf`、`PUT /api/admin/products/{id}`、`GET /api/admin/products/stock`、`GET /api/admin/summary`、`GET /api/admin/orders/{id}/records`
- 仓库：`GET /api/admin/ship/pending`、`POST /api/admin/ship/{id}`、`GET /api/admin/ship/shipped`
- 客服：`GET /api/admin/refunds/pending`、`POST /api/admin/refunds/{id}/approve`、`POST /api/admin/refunds/{id}/reject`、`POST /api/admin/refunds/{id}/retry`、`GET /api/admin/pay-abnormal`、`POST /api/admin/pay-abnormal/{id}/refund`
- 回调网关（演示）：`POST /api/admin/pay/mock-notify`

## 4. 页面与 RBAC 菜单（按原型 admin.html）

| 角色 | 菜单 | 对应页面 |
| --- | --- | --- |
| 运营 | 商品管理 / 库存看板 / 交易汇总 | views/product、views/summary |
| 仓库·商家 | 可发货订单 / 已发货记录 / 发货数据 | views/shipment |
| 客服 | 退款审核 / 异常支付 / 退款重试 | views/refund |

越权要求：仓库不可见退款审核/商品管理，客服不可见商品管理/发货登记；前端隐藏菜单 + 后端 403 双重校验。

## 5. Agent 职责与边界

- 开发 Agent：实现上述业务页面、`src/api` 封装、按角色过滤的路由；不得改核心框架目录结构；不得为了页面好看引入超出 Element UI 之外的重型依赖（需学生确认）。
- 测试/验收 Agent：独立会话，按原始题面核对五组场景在页面可走通、越权被拒、与后端接口一致。

## 6. 提交与分支规则

- 开发只提交 `dev/01`，禁止直推 `main`；`main` 只经 PR 合并。
- 提交格式：`[类型] [版本号] [编号] 中文描述`；feat/fix 须先 meegle 核验真实编号。
- 不提交 `node_modules`、`dist`（已有 .gitignore）。

## 7. 禁止事项

- 禁止把业务接口写死返回假数据冒充已联调；业务必须调用后端真实接口。
- 禁止泄露后端地址中的敏感信息；环境变量与密钥不入库。
- 禁止跳过登录鉴权或通过前端改角色绕过权限。

## 8. 版本记录

| 版本 | 日期 | 变更 |
| --- | --- | --- |
| v1 | 2026-11-15 | 初版：结构、接口、RBAC、Agent 职责 |
