# 取料

Selection 增加 type 列限制定向物资的选择
---------------------------------------------------------------------------
Selection 取料分为三个类别：普通取料、订单备货和客户备货.

- `selection/create` 取普通物资；
- `inventory-item/auto-select` 取订单备货物资；
- `match-hoard/pick` 取客户备货物资；

Change Logs
--------------------------------------------------------------------------
- 2024-12-30 `Adj` selection/micro-diamond. 微粉判定依据之前是 product.name, 现在改成 unit.sku.spu.type 更合理。
  因为前者存在这样一种情况：订单品名是微粉，但是实际发货的可能是整形料甚至是破碎料，
  进而导致对应的标准粒度栏空白的问题. 前期使用 `micro-diamond-from-customer` 进行区分，后期将会舍弃；
- 2023-11-13 `Schema` 新增 `profit` 列，保存外购料毛利润；
