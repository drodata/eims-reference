# 内容变更

这是一个通用模型，记录各个模型内容变更的过程。每次变更都需要评审来决定变更是否生效。

Schema
---------------------------------------------------------------------------
包含 `edition`, `edition_item` 和 `edition_audit` 三个表格.

### `edition`

Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`branch_id`                 | int       | No   | 
`name`                      | varchar   | No   | 模型 key. `demand-item` 等
`type`                      | int       | No   | `DELAY_DEADLINE` 等
`reason`                    | varchar   | No   | 
`status`                    | int       | No   | 1: 已创建 4: 已拒绝 9: 已完成

### `edition_item`

Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`edition_id`                | int       | no   | 
`name`                      | varchar   | no   | `edition.name` 表格中的列名
`value`                     | varchar   | yes  | 
`old_value`                 | varchar   | yes  | 

状态
---------------------------------------------------------------------------

状态常量                | 状态值 | 值改变场景
------------------------|--------|------------
`STATUS_CREATED`        |   1    | 
`STATUS_REFUSED`        |   4    | 终审拒绝时 
`STATUS_COMPLETED`      |   9    | 终审通过时

- 率先部署在采购清单的延期上
