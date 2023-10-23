# 备货 
`preparation` 的初衷是为了表示“订单中每个订货是如何准备的”。最初的设定是有四种备货形式：库存现货、二次加工、生产和市场采购。实际运行中，只使用“市场采购”一个类型(使用 `PurchasingPreparation`承载)

Schema
---------------------------------------------------------------------------
### Preparation Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`goods_id`                          | int       | No   | 订单明细
`is_live`                           | bool      | No   | 默认是 1, 当备货无效是为 0
`type`                              | int       | No   | Lookup. 
`quantity`                          | int       | No   | 备货数量
`status`                            | int       | No   | Lookup.

- 四种类型：库存发货、二次加工、生产和采购；
- 三种状态：草稿、正式的、最终的 (已舍弃，通过 `plan_item.status` 管理)；

### Purchasing Preparation Schema

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`status`                            | bool      | No   | Lookup.
`cycle`                             | int       | Yes  | 交货周期
`price`                             | decimal   | Yes  | 采购单价
`supplier`                          | string    | Yes  | 供应商
`note`                              | string    | Yes  | 
`is_generated`                      | bool      | Yes  | 是否生成对应的 demand item (需求明细)

- status (已舍弃，通过 `plan_item.status` 管理)

采购备货
---------------------------------------------------------------------------
仓库管理员通过 `purchasing-preparation/create` 新建。采购备货新建操作会同时在三个表中新建记录：`preparation`, `purchasing_preparationg` 和 `plan_item` (见 `Preparation::build()`).

流程要点：

- 仓管员在订单取料页面申请备货。创建后点击“确认” (`plan-item/confirm`)，转交采购员询价（采购员微信会收到询价工作提醒）；
- 采购员询价后生成新的 `PlanItem` 记录，供接下来的新建采购计划使用；
- 总经理评审采购计划，同意后，采购员需手动生成需求明细；如果不同意，直接将状态设置为不同意，结束。

