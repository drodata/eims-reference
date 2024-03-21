# Transfer 借调

相关角色及权限：
- `createTaansfer`: warehouseKeeper 拥有；
- `manageTransfer`: productionDirector, gwDirector, saleDirector, warehouseKeeper, cfo;
- `viewTransfer`: createTransfer, manageTransfer;

### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`source_branch_id`                  | int       | No   | 
`target_branch_id`                  | int       | No   | 
`status`                            | int       | No   | 已创建、已取料、已完成
`inventory_id`                      | int       | No   | 关联 inventory

### TransferItem Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`transfer_id`                       | int       | Yes  | 
`warehouse_id`                      | int       | No   | 
`unit_id`                           | int       | No   | 已创建、已取料、已完成
`quantity`                          | int       | No   | 关联 inventory
`price`                             | decimal   | Yes  | (11,5) 单价

操作
--------------------------------------------------------------------------
### 新建
有仓管提交。

### 评审
如果涉及制品中心，则需要评审，否则不需要。

接收仓管 (→ 销售经理) → 发出仓管 → 发出生产经理

### 确定单价
对于非采购类物资，需要销售经理录入单价至 `transfer_item.price`, 以备将来到处使用。

### 交付
`confirm-pick`. 发出部门仓管操作，仅起到标记作用。

### 入库
`store`. 接收部门仓管操作。入库后将生成对应的 inventory 记录，使库存生效。


变更日志
--------------------------------------------------------------------------
- 2024-03-21 `Enh` schema: 增加 `transfer_item.price`, 方便销售录入单价；
