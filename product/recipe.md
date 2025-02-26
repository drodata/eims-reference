# 食谱
主要用途：

1. 存储复合片加工单中毛坯到成品的映射；
2. 存储物资转换单中商品的对应关系；

结构
--------------------------------------------------------------------------
### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`type`                              | int       | No   | Lookup `reprocessing-type`(和 reprocessing 共用)
`input_id`                          | int       | No   | 投入 sku id 
`output_id`                         | int       | No   | 产出 sku id 
`rate`                              | int       | No   | 产出投入比率(默认值1) 
