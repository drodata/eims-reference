# 通用取料退回

通常情况下，物资取料后不可撤销。实际操作中，会出现取料的物资重新返回仓库的情况。比如复合片加工中。工人领取毛坯加工过程中，遇到制品厂盘库，需要收回所有毛坯和成品。这就需要先把已领出但未加工的毛坯退回仓库，仓库再新建出库单交给公司。

考虑到可扩展性，打算设计成通用的模型，取名“取料退回” (Withdrawal), 可以理解为[取料操作][generic-pickup]的逆操作。那个取料需要撤回，就新建关联表建立连接。关联表的名称可以命名为动词格式 (e.g. `withdraw_machining`). 

取料退回的核心属性包括：物资批号、仓库编号、退回数量。仓库签收入库后，此表和通用 Inventory 表再建立关联，实现库存变化记账操作。

结构
---------------------------------------------------------------------------

### 权限
- `requestWithdrawal`: 申请退回单。gwPieceWorker, gwMixer 拥有；
- `operateWithdrawal`: 操作退回单。warehouseKeeper, gwPieceWorker 和 gwMixer

### Schema Withdrawal 

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   | 
`name`                              | string    | No   | 区分类别的名称 key. 如 `machining`
`unit_id`                           | int       | No   | 
`warehouse_id`                      | int       | No   | 
`quantity`                          | int       | Yes  | 退回数量
`status`                            | int       | No   | 已创建(1)、已交付(3)、已完成(9)

### 关联表
以自加工退回关联表为例, 关联表 `WithdrawMachining`

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`machining_id`                      | int       | No   | 
`withdrawal_id`                     | int       | No   | 

其它部署的表格还包括：

- `withdraw_bucket_selection_trace`

操作
---------------------------------------------------------------------------
### 新建
### 交付

### 签收

> 领料交付计量单位问题
>
> 磨料和结合剂签收时单位都是“克”，这里入库时需要进行额外单位转换。

变更日志
--------------------------------------------------------------------------
- 2024-06-13 扩展, 承载领料交付的退回

[generic-pickup]: /models/pickup.md
