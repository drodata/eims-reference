# 首页

Change Logs
---------------------------------------------------------------------------

日期        | 大类              | 类别      | 动作 | 说明
------------|-------------------|-----------|------|-------------------
2023-06-28  | Requisition       |           | 新增 | 消耗品领用模型,承载低值易耗品和劳保物资的领用
2023-06-21  | BucketItem        | Schema    | 新增 | `measurement_unit` 单位列
2023-06-14  | Bucket            | Action    | 弃用 | `bucket/confirm-delivery`, BucketDelivery 增加签收操作后，此操作已意义；
2023-06-14  | BucketItem     | Action | 新增 | `bucket-item/tweak-quantity`, 评审后可以微调数量;


================ FOLLOWING IS THE TEMPLATE ==============

---------------------------------------------------------------------------
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`grinding_wheel_production_id`      | int       | No   | 生产编号
`grinding_wheel_code`               | int       | No   | 砂轮内部编码 Lookup
`mix_trace_id`                      | int       | Yes  | 追溯编码. `pack` scenario 时必填
`weight`                            | int       | Yes  | 混料总重量. `pack` scenario 时必填
`status`                            | int       | No   | 已创建(1)、已完成(9)
`type`                              | int       | Yes  | 预留列。目前只有砂轮混料这一种类别；

状态常量                | 状态值 | 值改变场景
------------------------|--------|------------
`STATUS_CREATED`        |   1    | 
`STATUS_REFUSED`        |   4    | 终审拒绝时 
`STATUS_COMPLETED`      |   9    | 终审通过时

