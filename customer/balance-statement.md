# 对账单

结构
---------------------------------------------------------------------
### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`customer_id`                       | int       | No   | 
`started_at`                        | int       | No   | 起始时间 
`ended_at`                          | int       | No   | 结束时间
`status`                            | int       | No   | Lookup 状态：
`deadline`                          | int       | Yes  | 对账截止时间
`receivable`                        | string    | Yes  | 
`advance`                           | string    | Yes  | 

### 状态
- 已创建 (DRAFT 1): 
- 已确认 (CHECKED 3):
- 已核实 (CONFIRMED 5):

操作
---------------------------------------------------------------------
### 创建
管理员通过 `./yii balance-statement/create YEAR` 批量生成帐单，其中 `YEAR`
表示对账年份，例如 `2023`.
### 确认
`balance-statement/check` 仅更新状态
### 复核
`balance-statement/confirm` 更新以下列：

- `status`
- `ended_at`
- `advance`
- `balance`

### 逾期未对账的限制

开关 `Yii::$ap->params['restrictionSwitches']['balanceStatement']` (common/configs) 用来控制对逾期未完成对账的限制。
此开关默认为开启。在下单页面(`order/create`)进行限制。
