# 重要客户流失

A,B类客户的 `recycles_at` 列值小于当前时间戳，即被视为流失。`recycles_at` 在下列情况下会更新值：

1. **客户大货订单交付**. `order-delivery/deliver` 内刷新；
2. 流失上报评审通过。(`Opinion::findlAudit()`)

概述
---------------------------------------------------------------------

### Outflow Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`customer_id`                       | int       | No   | 
`status`                            | int       | No   | Lookup 状态:“未上报”(1)、“已上报”(3)
`reason`                            | string    | Yes  | 流失原因
`action`                            | string    | Yes  | 采取的措施 
`deadline`                          | int       | Yes  | 评审截止时间

生成
---------------------------------------------------------------------
`./yii customer/outflow write` 定期执行生成。

上报
---------------------------------------------------------------------
业务员通过`outflow/report` 在指定时间内填写流失原因，并完成评审。

销售经理通过将调用 `Company::resetRecycling()` 重置回收时间（90天后）；拒绝将调用 `Company::reclaim()` 回收客户。
