# Sku

附加属性
---------------------------------------------------------------------------
包含：原生料、微粉、破碎料、整形料和 CBN 微粉五大类。

### Diamond Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | SKU ID
`maker`                             | int       | No   | Lookup `diamond-maker`
`strength`                          | int       | No   | Lookup `diamond-strength`
`level`                             | int       | No   | Lookup `diamond-level` (黄料、二黄料)
`size`                              | int       | No   | Lookup `diamond-size`

### MicroDiamond Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | SKU ID
`maker`                             | int       | No   | Lookup `diamond-maker`
`strength`                          | int       | No   | Lookup `diamond-strength`
`level`                             | int       | No   | Lookup `diamond-level` (黄料、二黄料)
`processing_object`                 | int       | No   | Lookup `micro-diamond-processing-object`
`standard_product_id`               | int       | No   | Product 标准粒度

### Rvd Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | SKU ID
`maker`                             | int       | No   | Lookup `diamond-maker`
`strength`                          | int       | No   | Lookup `diamond-strength`
`level`                             | int       | No   | Lookup `diamond-level` (黄料、二黄料)
`standard_product_id`               | int       | No   | Product 标准粒度

### Zxl Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | SKU ID
`maker`                             | int       | No   | Lookup `diamond-maker`
`strength`                          | int       | No   | Lookup `diamond-strength`
`level`                             | int       | No   | Lookup `diamond-level` (黄料、二黄料)
`standard_product_id`               | int       | No   | Product 标准粒度

### 设置属性
之前单独新建了一个控制器 `SkuAdditionalPropertyController`, 尝试在一个页面实现所有相关的设置页面。一直存在的一个问题是：`Model::loadMultiple()` 搜集的 tabular input 有一个限制，必须是同一种类型的模型. 这就带来一个问题：如果一个页面含有的附加商品种类多于一个，就会出现无法验证的情况。

解决方法很简单：新建一个 form model 接管这些类型 `SkuAdditionalPropertyForm`.
