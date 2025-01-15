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

