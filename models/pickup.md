# 通用取料模型

之前已经存在多个取料模型，直到设计取料退货 Withdrawal 时，才意识到可以把取料做成一个通用的模型，这样只需要有一个关联 inventory 表。

碰巧最近在改造代加工取料功能，决定新建通用模型来承载。要点如下：

1. `pickup` 表核心属性：`warehouse_id`, `unit_id`, `quantity` 和 `note`;
2. 关联表模型命名采用 `xxx_pickup` (`oem_item_pickup`), 连接宿主模型；
   库存写入使用 `pickup_inventory` 表连接；这种命名可以形成一个容易记忆的链条：
   `oem_item` - `oem_item_pickup` - `pickup` - `pickup_inventory`

### 已部署位置

1. 代加工取料
2. 调配单取料

模型结构
---------------------------------------------------------------------
### Pickup Schema
> `type` 列设计成整数不好，应该像 `audit.type` 那样设计成字符串类型。
> 好处时新增加类型时不必考虑增加 lookup 记录。
>
> 结论：如果不同类型的记录不会在一个页面内出现，就设置成字符串。

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`type`                              | int       | No   | Lookup 类型：代加工
`quantity`                          | int       | No   | 数量
`warehouse_id`                      | int       | Yes  | 
`unit_id`                           | int       | Yes  | 为了预留，这里默认值设置成可以为 null
`note`                              | string    | Yes  | 取料备注

- 关联表 `pickup_inventory`: 连接库存写入模块；
- 有的模型存在不需要取料的情况，此时 `unit_id` 和 `warehouse_id` 可以为空，
  即生成一条“假”取料记录。这点和领料取料类似 (BucketSelection);
