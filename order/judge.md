# 选料
选料操作在取料操作前，由 `skuJudge` 填写，目的是指导仓库取料工作。
简单来说，选料是“确定发什么料”，取料是“找到对应的料并取出来”。

其它要点：

- 尽管使用 `judge.goods_id` 外键约束，judge 和 goods 之间是一对一关系；

结构
--------------------------------------------------------------------------

### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`goods_id`                          | int       | No   | 
`content`                           | string    | Yes  | 
`first_candidate_id`                | int       | Yes  | 
`second_candidate_id`               | int       | Yes  | 
`third_candidate_id`                | int       | Yes  | 
`status`                            | int       | No   | Lookup 状态：已创建、已确认

操作
--------------------------------------------------------------------------

### 新建

`content` 和 `first_candidate_id` 两者至少填写一个，即要么确定批次，要么填写怎么做；

### 确定
confirm 操作仅改变状态为“已确认”。
