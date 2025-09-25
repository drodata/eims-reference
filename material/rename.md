# 物资改名

Structure
--------------------------------------------------------------------------

### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   | 
`old_unit_id`                       | int       | No   | 
`sku_id`                            | int       | No   | 
`new_unit_id`                       | int       | No   | 
`status`                            | int       | No   | Lookup `order-delivery-status` 

操作
--------------------------------------------------------------------------

### 检测

`detection/copy-portal` 页面允许质检直接复制旧批次的检测结果, 一般来水，会直接使用原批次对应的 Inspection 记录。一些批次早的物资没有对应的 Inspection 记录，检测结果在对应容器的成品检测（FinishedAnalysis 和 FinishedSem）内。对此， `Detection::copyFill()` 操作也会自动判断。

变更
--------------------------------------------------------------------------
- 2025-09-25 改进 复制检测结果操作，当找不到 Inspection 时，能自动从成品检测中复制检测结果；
