# 购买清单

此清单都是经过总经理确认、批准购买的物品。用于新建采购单时关联。

### 使用场景一
采购需求 Demand 的子条目
新建采购需求时，`demand_item.status` 默认值为 null. 
当采购需求被批准购买 (`Plan::decide()`)后，
再将 status 设置为 CREATED, 正式列入清单。**对于不批准购买的记录,
不再在 demand-item/index 显示**.

### 使用场景二

in Hoard, Pile and Preparation
这类清单需要采购员通过 `plan-item/generate` 手动生成.

Purchase::syncDemandItemStatus()
新建、修改、删除、入库

结构
---------------------------------------------------------------------------

### DemandItem Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   |
`type`                              | int       | No   | Lookup. 采购需求、订单备货、消耗品、公司或客户备货
`demand_id`                         | int       | Yes  | 需求单
`purchasing_preparation_id`         | int       | Yes  | 订单备货
`pile_id`                           | int       | Yes  | Pile
`plan_item_id`                      | int       | Yes  | 由于 Hoard 和 PlanItem 同时存在，用此为外键
`assigned_by`                       | int       | Yes  | 指派人
`name`                              | string    | No   | 
`quantity`                          | int       | No   | 
`unbought_quantity`                 | int       | Yes  | 
`deadline`                          | string    | Yes  | 
`bought_by`                         | int       | Yes  | 
`created_by`                        | int       | Yes  |
`price`                             | decimal   | Yes  | 选填项 
`supplier`                          | string    | Yes  | 选填项
`status`                            | int       | Yes  | 已创建、采购中、已完成
`frozen_level`                      | int       | No   | 已创建(1)、已完成(9)

### 类型

Type Constant           | Value     | Desc
------------------------|-----------|------------
`TYPE_DEMAND`           | 1         | 正常采购需求所生成
`TYPE_PREPARATION`      | 2         | 订单备货 (PlanItem)生成
`TYPE_PILE`             | 3         | 易耗品提前备货
`TYPE_HOARD`            | 4         | 原材料备货申请

### 状态

- 已创建 (1)
- 采购中 (3)
- 已完成、已处理 (5)

以下操作将触发关联购买清单的进度更新（`status` 和 `unbought_quantity`）：

1. 新建采购单
2. 修改采购单
3. 删除采购单
4. 新建退换货 (`refuse/fill`)
   
   仅针对处理方式是“退货”,换货不牵扯状态的变化。
5. 入库

操作
---------------------------------------------------------------------------

### 标记已完成
`mark-completed` (由 Editon 承载). 评审完成后触发 `Editon::applyChanges()`. 对本操作来说，就是把 `status` 列的值更新为已完成。

对于 `TYPE_PREPARATION` 类型的纪录，还需要额外的操作：将关联的需求清单状态设置为已作废。确保销售能正常修改订单。

`TYPE_PILE` 由于是系统自动生成，因此需要系统管理员终审。

### 删除
类型是 `TYPE_PREPARATION` 的订单备货，在采购未创建采购单之前，有删除的需求：部分订单的需求明细生成后，还想删除订单。这个时候就需要把之前的“创建备货单、询价、生成需求明细”全部倒着执行一遍.
### 询价
`inquire`. 部分需求单单价栏为空，采购员希望在评审计划前将单价补充进去。

demand item 的状态一旦被设置, 就不能再使用此操作。

变更日志
--------------------------------------------------------------------------
- 2025-02-06 `Enh` PILE 类型的清单也支持标记已完成操作；
- 2024-06-22 `Enh` Schema, 增加 `section_id` 列，精确到小部门；
