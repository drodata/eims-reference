# 砂轮生产

生产单
---------------------------------------------------------------------------

### GrindingWheelProduction
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`is_oem`                            | int       | No   | 默认为 0
`direction`                         | int       | Yes  | Lookup: POST (根据订单生产), PRE(提前备货) 
`deal_item_id`                      | int       | Yes  | 订单明细编号
`purpose`                           | string    | Yes  | 备货时必填
`grinding_wheel_id`                 | int       | No   | 砂轮编号
`label`                             | string    | Yes  | 
`quantity`                          | int       | No   | 
`status`                            | int       | No   | Lookup 状态：已创建(1)、生产中(2)、已完成(4)

- `is_oem` 列区分生产单类型是自产还是代工，代工单不牵扯领料,
   可直接交付.要求关联 `deal_item.name` 值必须是“代加工砂轮”；

领料
---------------------------------------------------------------------------
生产单创建后，可以申请原辅料。通过 Bucket 承载。

交付
---------------------------------------------------------------------------
### GrindingWheelProductionDelivery

### 数据导出
为确保数据安全性，仅 warehouseKeeper 可通过 `export/grinding-wheel-production-delivery` 下载交付明细表。

Change Logs
--------------------------------------------------------------------------
- 2023-12-15 Schema `Enh` 增加 `is_oem` 列，区分自产还是代工；
