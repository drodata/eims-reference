# 重要客户流失

A,B类客户的 `recycles_at` 列值小于当前时间戳，即被视为流失。`recycles_at` 在下列情况下会更新值：

1. **客户大货订单交付**. `order-delivery/deliver` 内刷新；
2. 流失上报评审通过。(`Opinion::findlAudit()`)

生成的流失记录，业务员须在 10 天内完成原因上报,逾期未上报将限制新建订单。

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

操作
---------------------------------------------------------------------

### 生成

`./yii customer/outflow write` 定期执行生成。

### 放弃
`outflow/discard`. 当前负责人可以放弃客户。放弃将：

1. 调用 `recycle()` 将客户放至回收站；
2. 删除当前 outflow 记录；

放弃客户后，当前负责人将不能从回收站再次认领该客户。

### 上报
业务员通过`outflow/report` 在指定时间内填写流失原因，并完成评审。

销售经理通过将调用 `Company::resetRecycling()` 重置回收时间（90天后）；拒绝将调用 `Company::reclaim()` 回收客户。
