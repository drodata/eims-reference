# 砂轮生产计划

结构
--------------------------------------------------------------------------
### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`is_legacy`                         | int       | No   | 新增 Fomula 后区分新旧
`is_oem`                            | int       | No   | 默认为 0
`grinding_wheel_id`                 | int       | No   | 砂轮编号
`formula_id`                        | int       | Yes  | 生产标准(`is_legacy` 为 0 是必填)
`quantity`                          | int       | No   | 
`direction`                         | int       | Yes  | Lookup: POST (根据订单生产), PRE(提前备货) 
`status`                            | int       | No   | Lookup 状态：已创建(1)、生产中(2)、已完成(4)
`deal_item_id`                      | int       | Yes  | 订单明细编号
`purpose`                           | string    | Yes  | 备货时必填
`label`                             | string    | Yes  | 

- `is_oem` 列区分生产单类型是自产还是代工，代工单不牵扯领料,
   可直接交付.要求关联 `deal_item.name` 值必须是“代加工砂轮”；
