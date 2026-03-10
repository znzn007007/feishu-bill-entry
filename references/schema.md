# Schema

## Target

- Recommended table name: `账单明细`
- The skill depends on a standard field structure, not on any fixed app id or table id.
- If the user already has a compatible table, use that table instead of forcing a rename.

## Required structured fields

- `日期` — DateTime
- `收支类型` — SingleSelect
- `金额` — Number
- `一级分类` — SingleSelect
- `渠道` — SingleSelect
- `账本` — SingleSelect
- `备注` — Text

## Review summary fields

Use the same seven fields in the user-facing review summary:
- 收支类型
- 金额
- 日期
- 一级分类
- 渠道
- 账本
- 备注

## Enums

### 收支类型
- 收入
- 支出

### 一级分类：支出
- 餐饮
- 交通
- 日用购物
- 居住缴费
- 医疗健康
- 社交人情
- 娱乐消费
- 旅游出行
- 学习成长
- 数字服务
- 其他支出

### 一级分类：收入
- 工资
- 报销
- 副业
- 投资收益
- 转账收入
- 退款
- 奖金
- 其他收入

### 渠道
Recommended initial options:
- 支付宝
- 微信
- 招商银行卡
- 中信信用卡
- 交通银行信用卡
- 交通银行卡
- 现金
- 其他

Normalization guidance:
- `微信零钱` → `微信`
- `交行借记卡` → `交通银行卡`

### 账本
Recommended initial options:
- 私账
- 公账
- 家庭账
- 其他

## Defaults

- recommended table name: `账单明细`
- default `账本`: `私账`
- default granularity: one transaction per entry
- mixed-order details go into `备注`
- if not explicitly confirmed, do not write

## Primary field guidance

Feishu tables require a primary field. It may be used as a display summary field.

Recommended display format:
- `YYYY-MM-DD 收支类型 金额 备注/商户`

Example:
- `2026-03-09 支出 16.00 新川小厨`

Keep `日期` as a separate structured field even if the primary field also includes date text.

## Public-release guidance

For a public ClawHub release:
- do not hardcode a real app token
- do not hardcode a real table id
- do not assume a specific user account or private table exists
- treat the table name and channel/account values as recommended defaults, not fixed private bindings
