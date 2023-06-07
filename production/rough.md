# 半成品

装块
---------------------------------------------------------------------------
### RoughItem
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

- 由于专门为历史遗留块新建了特殊的混料单，所以 `is_legacy` 可以删除；

新建
---------------------------------------------------------------------------
### Rough
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`trace_id`                          | int       | No   | 追溯编号.此处外键是 `trace` 表
`status`                            | int       | No   | 已创建(1)、已完成(9)
`type`                              | int       | Yes  | Reserved
