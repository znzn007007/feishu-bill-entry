# Examples

## Single natural-language expense

Input:
`午饭 28 支付宝`

Review summary:
- 收支类型：支出
- 金额：28.00
- 日期：今天
- 一级分类：餐饮
- 渠道：支付宝
- 账本：私账
- 备注：午饭

## Single natural-language income

Input:
`收到工资 12000 招商银行卡`

Review summary:
- 收支类型：收入
- 金额：12000.00
- 日期：今天
- 一级分类：工资
- 渠道：招商银行卡
- 账本：私账
- 备注：工资

## Clearly line-separated multiple entries

Input:
`午饭 28 支付宝
打车 35 微信
咖啡 19.9 支付宝`

Behavior:
- Split into 3 candidate records
- Return 3 review summaries
- Do not write until the user confirms

## Screenshot-based entry

Input:
- payment screenshot

Behavior:
- infer expense/income
- infer amount, date/time, channel, category, merchant
- return standard review summary only

## Edit then confirm

First review summary:
- 收支类型：支出
- 金额：188.00
- 日期：2026-03-09
- 一级分类：餐饮
- 渠道：中信信用卡
- 账本：私账
- 备注：请客吃饭

User reply:
`分类改成社交人情，确认`

Expected behavior:
- revise category to `社交人情`
- treat the same message as confirmation
- write after revision because explicit confirmation is present

## Non-confirming edits

User reply:
`渠道不对，改成微信`

Expected behavior:
- update the summary
- do not write yet

## Mixed order that should not be auto-split

Input:
`今天一共花了300，吃饭打车买咖啡都有`

Expected behavior:
- do not aggressively split
- keep as one candidate entry or ask for clarification if needed
- place detail into `备注`
