# Buyer 客户

结构
---------------------------------------------------------------------
### Buyer Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   | 
`name`                              | string    | No   | 
`short_name`                        | string    | Yes  | 
`contacter`                         | string    | No   | 关键联系人
`phone`                             | string    | No   | 
`receivable`                        | decimal   | Yes  | 
`enable_advance`                    | bool      | No   | 
`advance`                           | decimal   | Yes  | 
`status`                            | int       | No   |预留
`owned_by`                          | int       | No   | 负责人
`assisted_by`                       | int       | Yes  | 内勤 

操作
---------------------------------------------------------------------
### 分配
`assign` 分配内勤

### 收回
`take-back`
