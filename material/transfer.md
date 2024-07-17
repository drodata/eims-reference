# Transfer 借调

物资借调用来记录部门间物资的借调，可用于各部门成本核算。

相关角色及权限：
- `createTaansfer`: materialKeeper 拥有；
- `manageTransfer`: productionDirector, gwDirector, saleDirector, materialKeeper, cfo;
- `viewTransfer`: createTransfer, manageTransfer;

### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`source_branch_id`                  | int       | No   | 
`target_branch_id`                  | int       | No   | 
`source_section_id`                 | int       | No   | 发出部门
`target_section_id`                 | int       | No   | 接收部门
`status`                            | int       | No   | 已创建(1)、已交付(3)、已作废(4)、已完成(5)
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

> 申请人 (→ 销售经理) → 发出部门物资保管员 → 发出部门生产经理

- 销售经理参与的目的是确认借调物资的单价,方便将来部门成本核算；

### 确定单价
对于非采购类物资，需要销售经理录入单价至 `transfer_item.price`, 以备将来到处使用。

### 交付
`confirm-pick`. 发出部门仓管操作，仅起到标记作用。
### 作废
在评审通过后、入库前的这段时间，申请人可以申请作废单据。

> 申请人 →  发出部门物资保管员 → 发出部门生产经理

### 入库
`store`. 接收部门仓管操作。入库后将生成对应的 inventory 记录，使库存生效。


变更日志
--------------------------------------------------------------------------
- 2024-07-17 `Add` 新增作废操作
- 2024-06-18 `Enh` schema: 增加 `source_section_id` 和 `target_section_id`, 更改为以部门 (Section) 为单位的借调；
- 2024-03-21 `Enh` schema: 增加 `transfer_item.price`, 方便销售录入单价；
