# 对账单

结构
---------------------------------------------------------------------
### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`seller_id`                         | int       | No   | 
`started_at`                        | int       | No   | 起始时间 
`ended_at`                          | int       | No   | 结束时间
`status`                            | int       | No   | Lookup 状态：
`deadline`                          | int       | Yes  | 对账截止时间
`prepayment`                        | string    | Yes  | 预付金额
`payable`                           | string    | Yes  | 应付金额

### 状态
- 已创建 (UNCHECKED 1): 
- 已确认 (CHECKED 2):
- 已核实 (CONFIRMED 3):

操作
---------------------------------------------------------------------
### 创建
管理员通过 `./yii payable-statement/create YEAR` 批量生成帐单，其中 `YEAR`
表示对账年份，例如 `2023`.
### 确认
`payable-statement/submit-check` 仅更新状态
### 复核
`payable-statement/confirm` 更新以下列：

- `status`
- `ended_at`
- `prepayment`
- `payable`

### 逾期未对账的限制

开关 `Yii::$ap->params['restrictionSwitches']['payableStatement']` (common/configs) 用来控制对逾期未完成对账的限制。
此开关默认为开启。在下单页面(`purchase/create`)进行限制。
