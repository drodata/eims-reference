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
`formula_id`                        | int       | No   | 生产标准
`formula_check_switch`              | int       | Yes  | 生产标准检查开关
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
### Schema GrindingWheelProductionDelivery
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`grinding_wheel_production_id`      | int       | No   | 
`quantity`                          | int       | No   |
`status`                            | int       | No   | Lookup 状态：
`purpose`                           | int       | No   | Lookup 状态：自用、销售

- 'purpose' 列是中途新增的，因此早期记录中该列的值是 null;

操作
---------------------------------------------------------------------------

### 确认
确认后更新状态为“已确认”。若生产单开始时间是当天，还将自动生成对应的领料单。

### 数据导出
为确保数据安全性，仅 warehouseKeeper 可通过 `export/grinding-wheel-production-delivery` 下载交付明细表。

### 关闭标准检查
`grinding-wheel-production/turn-off-formula-check`. 由生产经理操作.

新建混料单时，系统会对混料员选取的物料和生产单关联的生产标准进行校验：
从粒度、数量等方面逐一核对,发现问题后禁止保存混料单。

实际操作中，存在某些特殊情况，使得在不满足生产标准的情况下，仍然创建混料单。
基于此，增加 `grinding_wheel_production.formula_check_switch` 布尔列。
此列默认值为 1. 当存在特殊情况时，混料员可请求生产经理关闭此开关，
从而绕过检查。**注意：混料单保存后会立刻重新开启此开关**,
即关闭后**仅可提交一次**此标准下的生产单。

Change Logs
--------------------------------------------------------------------------
- 2025-05-08 `Add` `grinding-wheel-production/turn-off-formula-check`. 通过此开关，在混料单新建环节允许绕过标准检查；
- 2025-05-08 `Schema` 新增 `grinding_wheel_production.formula_check_switch`
- 2025-01-03 `Enh` 新建操作，允许创建当天开始的生产单（会在确认操作时自动创建领料单）；
- 2024-01-04 `Enh` Schema: `grinding_wheel_production_delivery.purpose` 列，记录是自用还是销售
- 2023-12-15 `Enh` Schema: 增加 `is_oem` 列，区分自产还是代工；
