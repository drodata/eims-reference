# 月结帐单

结构
---------------------------------------------------------------------------

### Billing Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`customer_id`                       | int       | No   | 
`period`                            | string    | No   | '201105'
`is_normal`                         | int       | No   | 默认是 1
`amount`                            | string    | Yes  | 
`charge`                            | string    | Yes  | 
`deadline`                          | int       | Yes  | 还款截止日期
`confirm_expires_at`                | int       | Yes  | 客户确认截止日期
`status`                            | int       | No   | 未付款(1)、已付款(2)

### Modern Billing

订单增加”终止交付“操作后，按照 `goods` 记录统计帐单明细就会出现问题。更合理的办法是按照订单交付的明细来统计：以 `order_delivery.delivered_at` 作为统计标准。

增加 `Billing::getIsModern()` 将帐单分成两类： modern billing 按照订单交付明细统计，反之 legacy billing 仍按照 Goods 统计。

从 202308 账期开始都视为 modern billing.

退货按照入库时间（目前没有单独列存储，以 `reject.updated_at` 为标准）算到相应帐单内。

---------------------------------------------------------------------------
Column | Type | Note
--------|----------|-------
`id` | int | unit id
`plan_item_id` | int |

状态常量 | 状态值 | 值改变场景
--------|----------|-------
`STATUS_CREATED` | 1 | 新建记录


Change Logs
---------------------------------------------------------------------------
日期        | 类别      | 动作 | 说明
------------|-----------|------|-------------------
2023-09-18  | Logic     | 调整 | 统计标准改成已交付明细为准而不是订单明细
