# 内容变更

这是一个通用模型，记录各个模型内容变更的过程。每次变更都需要评审来决定变更是否生效。
包含 `edition`, `edition_item` 两个表格，和 Audit 类似，通过关联表`edit_xxx` 和宿主模型建立关联。

Edition
---------------------------------------------------------------------------

Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`branch_id`                 | int       | No   | 
`name`                      | varchar   | No   | 模型 key. `demand-item` 等
`type`                      | int       | No   | `DELAY_DEADLINE` 等
`reason`                    | varchar   | No   | 
`status`                    | int       | No   | 1: 已创建 4: 已拒绝 9: 已完成


### 名称
### Editon Type
- **标记已完成** `TYPE_MARK_COMPLETED` = 1.
- **延期** `TYPE_DELAY_DEADLINE` = 2
- **调整数量** `TYPE_TWEAK_QUANTITY` = 3
- **作废** `TYPE_DISCARD` = 4
- **变更检测项目** `TYPE_CHANGE_INSPECTION_METHODS` = 5
- **设置无需取料** `TYPE_TOGGLE_PICKNESS` = 6
  
  BucketItem 和 OemItem 设置为无需取料；
- **终止交付** `TYPE_TERMINATE` = 14
- **取消留样** `TYPE_REVOKE_SPECIMEN` = 21
- **变更备注** `TYPE_TWEAK_NOTE` = 51

### 状态

状态常量                | 状态值 | 值改变场景
------------------------|--------|------------
`STATUS_CREATED`        |   1    | 
`STATUS_REFUSED`        |   4    | 终审拒绝时 
`STATUS_COMPLETED`      |   9    | 终审通过时

EditionItem
---------------------------------------------------------------------------

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

部署列表
---------------------------------------------------------------------------

- 率先部署在采购清单的延期上
- 原辅料清单明细支持修改数量；
- 订单交付明细中变更检测项目；

### 实现步骤

0. (可选) host 表增加状态值 (e.g. bucket status ended)
1. 搭建关联表 `edit_xxx` (新 name 时)
    - `edition.type` lookup 更新 (新 type 时)
    - Gii 关联表模型
3. Edition
    - 新增 name 和 type 常量
        - `getIsXxx()`
    - `getHost()` (新 name 时)
    - `getJunction()` (新 name 时)
    - `insertJunction()` (新 name 时)
    - `getEditionForm()`: 让表单页面正常；
    - `hostParams()` (新 name 时)
    - `saveItems()` (新 type 时)
    - `applyChanges()` 修改生效的逻辑代码 (新 name 或 type 时)
    - `getNextAuditorList()`: 下一个评审人（类似只有一个评审人的比较简单的评审可以跳过）
2. Host：
    - `init()`: 增加 `deleteEditions()` handler;
    - `getEditions()`;
    - `getDataProvider()` 增加 `editions` section;
    - `getActionOptios()` 增加 对应按钮
4. EditionForm
    - 新增 scenario (新 type 时)
3. Lookup
    `auditors()` 设定首个审批人
5. AuditForm
    - `validateIsFinal()` (类似只有一个评审人的比较简单的评审可以跳过)
3. EditionController
    - 订单增加 `use AR` (when new NAME)
    - 改进 `findEditionHost()` (新 name 时)
5. views
    - host's `_detail-action` view, adds button
    - `edition/_form` (when there is a new type)
    - `edition/_detail-audit` (新 type 时)
