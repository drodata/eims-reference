# 订单取料

结构
--------------------------------------------------------------------------
### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`section_id`                        | int       | Yes  | 所属部门
`type`                              | bool      | Yes  | DEFAULT(1), PREPARATION(2), HOARD (3)
`match_hoard_id`                    | int       | Yes  |
`preparation_id`                    | int       | Yes  |
`flag`                              | bool      | Yes  |
`goods_id`                          | int       | No   | 
`unit_id`                           | int       | No   | 
`warehouse_id`                      | int       | Yes  | 
`order_delivery_id`                 | int       | Yes  | 
`quantity`                          | int       | No   | 
`profit`                            | dedimal   | Yes  | 

### 权限
角色/权限               | Children                  | Assignment
------------------------|---------------------------|-----------------------
`manageSelection`       |                           | warehouseKeeper, gaWarehouseKeeper, productionDirector, gaDirector, qualityChecker, saleDirector

### Selection 增加 type 列限制定向物资的选择
Selection 取料分为三个类别：普通取料、订单备货和客户备货.

- `selection/create` 取普通物资；
- `inventory-item/auto-select` 取订单备货物资；
- `match-hoard/pick` 取客户备货物资；

操作
--------------------------------------------------------------------------
### 新建
研磨膏类产品取料具有特殊性：需要按照浓度进行折算取对应的微粉(仅限自己生产的情况).
通过 `Goods::requirePurity()` 进行判断。目前适用此情况的产品有：

- 金刚石研磨膏
- CBN 研磨膏

### 制品2部取料
product.name 值是组团磨料和组团磨料研磨液时，转交制品二部仓管进行取料和相关的订单备货操作。

借助 `Goods:getSelectionHint()` 判断。


Change Logs
--------------------------------------------------------------------------
- 2025-02-13 `Schema` 新增 `section_id` 列，记录所属部门；
- 2025-02-13 `RBAC` 新增 `manageSelection` 权限；
- 2025-01-20 `Enh` 新增产品名称——组团磨料研磨液, 由制品2部单独取料；
- 2025-01-15 `Enh` CBN 研磨膏也进行浓度折算；
- 2024-12-30 `Adj` selection/micro-diamond. 微粉判定依据之前是 product.name, 现在改成 unit.sku.spu.type 更合理。
  因为前者存在这样一种情况：订单品名是微粉，但是实际发货的可能是整形料甚至是破碎料，
  进而导致对应的标准粒度栏空白的问题. 前期使用 `micro-diamond-from-customer` 进行区分，后期将会舍弃；
- 2023-11-13 `Schema` 新增 `profit` 列，保存外购料毛利润；
