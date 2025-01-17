# 对账单
销售内勤管理的客户，对应的帐单由内勤而不是销售完成对账工作。

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
`delay_count`                       | int       | No   | 延期操作计数
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
管理员在年初通过 `./yii balance-statement/create YEAR` 批量生成帐单，
其中 `YEAR` 表示对账年份，例如 `2023`. 默认对账期限是 15 天。

### 确认
`balance-statement/check`. 业务员确认(更新状态为“已确认”)

### 复核
`balance-statement/confirm` 财务复核。更新以下列：

- `status`: 更新为“已核实”
- `ended_at`: 记录帐单结束时间戳
- `advance`: 当前预收金额
- `balance`: 当前应收金额

### 延期
`balance-statement/delay`. 因特殊原因未完成对账工作的，财务主管可通过此操作推迟对账截止日期.

`delay_count` 列控制延期上限。目前的上限是 2. 即最多可延期两次。

### 下单限制总开关

`Yii::$ap->params['restrictionSwitches']['balanceStatement']` (common/configs)
用来控制对逾期未完成对账的限制。

- 此开关**默认为开启**
- 在下单页面(`order/create`)进行限制;
- 此限制**仅在生产环境下使用**。

对于特殊的情况，可到 `common/config/params-local.php` 内全局关掉下单限制。
