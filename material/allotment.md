# Allotment 调拨

> :bell: 暂停使用.由 Transfer 承载
>
> 在 `transfer` 表增加 `source_section_id` 和 `target_section_id` 列后就能实现此功能。
> 换句话说，Transfer 由原来的账套之间更进一步，精确到账套内的部门 (Section) 之间。

新增部门(Section)后，记录物资在同一账套内、不同部门间的流动。

结构
---------------------------------------------------------------------
### Allotment Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   | 账套
`source_section_id`                 | int       | No   | 发出部门
`target_section_id`                 | int       | No   | 接收部门
`status`                            | int       | No   | Lookup 状态：已创建(1)、已交付(3)、已完成(9)

### AllotmentItem Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`allotment_id`                      | int       | No   | 调拨单号
`name`                              | int       | No   | 品名
`specification`                     | string    | No   | 规格
`quantity`                          | int       | No   | 数量
`measurement_unit`                  | int       | No   | 单位

### 权限
* createAllotment: materialKeeper 拥有
* manageAllotment: productionDirector 和 gwDirector 拥有
* viewAllotment: createAllotment 和 manageAllotment 的子权限

### 评审
> 接收部门 materialKeeper → 发出部门 MaterialKeeper → 发出部门生产经理

操作
---------------------------------------------------------------------
### 取料
`pickup-portal`. 借助通用 Pickup 模型实现（通过 `allotment_item_pickup` 连接）。
### 交付
`deliver`. 变更状态为“已交付”。
### 入库
`store`. 计入库存变化。
