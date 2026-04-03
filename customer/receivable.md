# 账单

结构
--------------------------------------------------------------------------
### receivable

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   | 
`type`                              | int       | No   | 交付、期初结转
`host_id`                           | int       | No   | 
`customer_id`                       | int       | Yes  | 
`buyer_id`                          | int       | Yes  | 
`currency`                          | bool      | No   | 
`exchange_rate`                     | decimal   | No   | 
`original_amount`                   | decimal   | No   | 原币初始欠款
`remaining_amount`                  | decimal   | No   | 原币当前欠款
`local_amount`                      | decimal   | No   | 本位币当前欠款
`status`                            | int       | No   | 1, 9, 14 创建、完成、作废
`period`                            | string    | Yes  | 账期 '201105'
`deadline`                          | int       | No   | 初始截止日期
`current_deadline`                  | int       | No   | 当前截止日期(实际统计依据)
`defer_count`                       | bool      | No   | 延期次数

- 单笔结算客户：交付单 deliver 环节直接写入 'period';
- 月结客户：交付时 period 保持 null, 标记“已发货、未入账”特殊情况，次月初统一设置；

变更
--------------------------------------------------------------------------
- 2026-04-03 调整 schema: 新增 `defer_count` 和 `current_deadline`. 支持延期
