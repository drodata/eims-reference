# 内容变更

这是一个通用模型，记录各个模型内容变更的过程。每次变更都需要评审来决定变更是否生效。

Schema
---------------------------------------------------------------------------
包含 `edition`, `edition_item` 两个表格，和 Audit 类似，通过关联表`edit_xxx` 和宿主模型建立关联。

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

### 外键约束设计方式

之前模型和通用 Audit 都是通过专门的关联模型如 OrderAudit 连接，这种设计的问题在于每次扩展起来相对起来麻烦。这次设计 Edition 时打算使用**多列外键**的设计思路，就是通过在 `audit` 表上不断增加外键来实现扩展。比如现在的的场景，通过新增 `audit.edition_id` 把 Edition 和 Audit 连接起来。

实际操作中发现添加外键的时间很长, 这是因为 audit 记录很多。立刻意识到这种设计思路的弊端——查询的效率会很低。还是之前的方式更可取，尽管关联表格很多，但是没有性能问题。

但是 Edition 和 Audit 的区别在于： Edition 关联表写入发生在 Edition 写入后；而 Audit 的关联表发生在宿主模型建立后。

状态
---------------------------------------------------------------------------

状态常量                | 状态值 | 值改变场景
------------------------|--------|------------
`STATUS_CREATED`        |   1    | 
`STATUS_REFUSED`        |   4    | 终审拒绝时 
`STATUS_COMPLETED`      |   9    | 终审通过时

部署列表
---------------------------------------------------------------------------

- 率先部署在采购清单的延期上
- 原辅料清单明细支持修改数量；
