# 外购备货

> :warning:
> 
> - `cycle`, `price` 等列已弃用，在 `plan_item` 表内存储。这些列将在将来彻底删除。
> - `status` 也不再适用，统一在 plan item 内管理状态。
>

结构
--------------------------------------------------------------------------
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

操作
--------------------------------------------------------------------------
### 新建
仓库管理员通过 `purchasing-preparation/create` 新建。采购备货新建操作会**同时在三个表中新建记录**：`preparation`, `purchasing_preparationg` 和 `plan_item` (见 `Preparation::build()`).

后续的操作都在 PlanItem 上进行。
