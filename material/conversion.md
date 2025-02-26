# 单位转换
结合剂采购的单位是千克，实际使用中以克为单位出库.为此创建此模型，承载单位换算的过程。

首先是存储换算的两个 sku 之间的对应关系，通过 `sku/set-measurement-unit-conversion-target` 操作完成。**该操作按钮放在对应的结合剂物资详情页面**，由 root 操作。

结构
--------------------------------------------------------------------------
### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`unit_id`                           | int       | No   | 
`material_id`                       | int       | No   | 
`recipe_id`                         | int       | No   | 
`quantity`                          | bool      | No   | 本地转换数量
`status`                            | int       | No   | Lookup `conversion-status`: 已创建(1)、已检测(5)、已完成(9)

操作
--------------------------------------------------------------------------
### 新建
`material/convert-measurement-unit`. 入口在需要转换的物资详情页面. 主要采集需要转换的数量。

### 检测
`conversion/send-inspect` 一键检测。因为仅仅是更换单位，这里直接借用原物资的检测数据。

- 批次类型不变，仍然是采购,且物资详情页“采购过程” Tab 显示原物资的相关信息；
- 通过 `detect_conversion` 关联表存储检测信息，`detection.amount` 记录换算后的数量；

### 确认
`converson/apply`. 执行转换操作,标记状态为“已完成”。

内部通过 `conversion_inventory` 记录库存变动。
