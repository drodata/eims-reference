# Material

### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`unit_id`                           | int       | No   | 
`warehouse_id`                      | int       | No   | 生产编号
`area_id`                           | int       | Yes  | 库位 Reserved
`stock`                             | int       | No   | 库存
`threshold`                         | int       | No   | 预警值
`cost`                              | int       | Yes  | 成本价 (采购的物资)
`stored_at`                         | int       | Yes  | 库龄

- `unit_id` 和 `warehouse_id` 复合主键；

Change Logs
--------------------------------------------------------------------------
- 2023-11-10 `Schema` 新增 `cost` 和 `stored_at` 两列，为订单取料单价检查做准备；
