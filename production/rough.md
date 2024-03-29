# 半成品

结构
---------------------------------------------------------------------------
### RoughItem Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`rough_id`                          | int       | Yes  | 半成品编号
`is_legacy`                         | int       | No   | 是否是历史遗留部件
`name`                              | int       | No   | 部件名称。这里留有余地，目前都是磨料块
`quantity`                          | int       | No   | 数量。
`mix_trace_id`                      | int       | No   | 混料追溯编号
`presser`                           | int       | No   | 压块人
`note`                              | string    | Yes  | 预留文本备注

### Rough Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`trace_id`                          | int       | No   | 追溯编号.此处外键是 `trace` 表
`status`                            | int       | No   | 已创建(1)、已作废(4)、已完成(9)
`type`                              | int       | Yes  | Reserved

- 由于专门为历史遗留块新建了特殊的混料单，所以 `is_legacy` 可以删除；

### 状态

操作
---------------------------------------------------------------------------

### 装块
通过 `rough-item/create` 选择合适的块，每次操作都会扣除库存。
### 新建
通过 `rough/create`. 主要是填写追溯码。

### 作废
`rough/discard`. 有的时候压块记录中的混料追溯码会出现选错的情况。通过此操作可将装块操作撤回。

撤回后之前填写的追溯码设置为“已归档”，同时更新磨料块库存。

变更日志
--------------------------------------------------------------------------
- 2024-01-19 `Add` `rough/discard` 作废操作,应对录入错误的问题；
