---
name: feishu-bill-entry
description: Feishu 多维表格记账录入技能。用于接收账单截图、分行多笔文本、单笔自然语言、或文本+截图补充，生成标准记账摘要，允许用户先修改，再在用户明确确认后写入 Feishu Bitable。适用于个人记账、收入支出录入、账单结构化入表、以及确认后写入的场景。
---

# Feishu Bill Entry

## Overview

Use this skill to turn screenshots or natural-language bill inputs into structured Feishu Bitable records. Generate a standard summary first, allow edits, and write only after explicit confirmation.

## Prerequisites

Before using this skill, make sure all of the following are true:

- Feishu official plugin is installed and working.
- The Feishu app has permission to access the target Bitable.
- The target Bitable table has the required standard fields, or the user is willing to create them.
- The target table has been shared to the Feishu app.
- **Configuration is required**: create `~/.openclaw/workspace/config/feishu-bill.json` before using this skill.

Use this config shape:

```json
{
  "app_token": "YOUR_APP_TOKEN",
  "table_id": "YOUR_TABLE_ID",
  "table_name": "账单明细"
}
```

Recommended table name: `账单明细`.

This skill depends on the standard field structure, not on a fixed app id or table id.

## Workflow

1. Determine whether the input is one of these supported forms:
   - single screenshot
   - multiple screenshots
   - single natural-language entry
   - clearly line-separated multiple entries
   - text + screenshot correction
2. Extract a candidate record.
3. Return only the standard summary to the user.
4. If the user asks for changes, revise the summary first.
5. Write to Feishu Bitable only when the user explicitly confirms.

## Hard Rules

- Do not auto-write after generating a summary.
- Do not treat “改一下/看看/check” as confirmation.
- Write only after explicit confirmation such as “确认 / 可以写入 / 就这样，入表”.
- Return only the summary during the review step; avoid extra narration.
- Default to one record per payment/receipt.
- Support clearly line-separated multiple entries, but do not aggressively split ambiguous combined text.
- When a mixed order cannot be reliably split, keep one record and place details in `备注`.

## Target Table Contract

Use this field contract:
- `日期`
- `收支类型`
- `金额`
- `一级分类`
- `渠道`
- `账本`
- `备注`

A Feishu table requires a primary field. The primary field may be used as a display/summary field, but the structured fields above remain the source of truth.

See `references/schema.md` for the standard schema and enums.

## Defaults

- recommended table name: `账单明细`
- default `账本`: `私账`
- default write granularity: transaction-level
- default channel strategy: structured single-select values, not free text
- recognition strategy: OCR + AI when image text extraction is available; otherwise use multimodal interpretation

## Summary Format

Return this exact shape during review:

- 收支类型：
- 金额：
- 日期：
- 一级分类：
- 渠道：
- 账本：
- 备注：

Do not append operational notes unless the user asks.

## Entry Rules

### Natural language

Support short direct inputs such as:
- `午饭 28 支付宝`
- `收到工资 12000 招商银行卡`
- `猫粮 342.22 中信信用卡 记日用购物`

For multiple entries, only split when the entries are clearly separated by lines or equivalent separators.

### Screenshots

Infer:
- income vs expense
- amount
- date/time
- channel
- likely category
- merchant/remark

If confidence is weak, still return a candidate summary for review instead of writing.

## Classification Policy

Keep only one-level categories.

Expense categories:
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

Income categories:
- 工资
- 报销
- 副业
- 投资收益
- 转账收入
- 退款
- 奖金
- 其他收入

Put extra detail into `备注` rather than inventing new categories.

## Channel Policy

Use structured single-select channels. Prefer existing configured values. If a channel is not yet configured, map to `其他` until the schema is updated.

## Minimal Setup Path

1. Prepare a Feishu Bitable table.
2. Ensure it contains the standard fields required by this skill.
3. Share the table to the Feishu app.
4. Create `~/.openclaw/workspace/config/feishu-bill.json` and fill in `app_token` and `table_id`.
5. Install/use this skill.
6. Send a screenshot or natural-language bill.
7. Review the generated summary.
8. Confirm explicitly to write the record.

## References

- Read `references/schema.md` for field meanings, enums, and defaults.
- Read `references/examples.md` for canonical examples before changing summary style or confirmation flow.
