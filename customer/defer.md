# 延期账单
本质：通过评审的方式，去修改 receivable 表中 `current_deadline` 和 `defer_count` 两列的值。

结构
--------------------------------------------------------------------------
### defer

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   | 
`customer_id`                       | int       | No   | 
`duration`                          | bool      | No   | 延期时长 Lookup `defer-duration`
`reason`                            | int       | No   | 
`status`                            | int       | No   | 1, 9, 14 创建、完成、作废

### defer_item

核心就是记录“那个账单，从A延期到B”
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`defer_id`                          | int       | No   | 
`receivable_id`                     | int       | No   | 
`old_deadline`                      | int       | No   | 
`new_deadline`                      | int       | Yes  | 

变更
--------------------------------------------------------------------------
- 2026-04-03 新增 
