# 登记回收

结构
---------------------------------------------------------------------
### Fixture Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   | 核算部门
`type`                              | int       | No   | Lookup. 登记(1)和回收(2)
`checked_by`                        | int       | Yes  | 检验员
`status`                            | int       | No   | Lookup. 已创建(1)、已送检(2)、已检测(3)、已完成(5)

### Fixture Item Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`fixture_id`                        | int       | No   | 
`sku_id`                            | int       | No   | 
`quantity`                          | int       | No   | 
`inspection_methods`                | set       | Yes  | 

### 权限

名称                        | 说明
----------------------------|---------------
`viewRegisteredFixture`     | 查看物资登记列表. warehouseKeeper, qualityChecker 和 hoardPurchaser 拥有；
`viewRecycledFixture`       | 查看物资回收列表. (warehouseKeeper, qualityChecker)
`viewFixture`               | 查看物资 base permission.
`createRegisteredFixture`   | 新建物资登记 (warehouseKeeper, hoardPurchaser)
`createRecycledFixture`     | 新建物资回收 (warehouseKeeper)
`createFixture`             | 查看 base permission. 用于控制 fixture update/delete 操作

物资登记
---------------------------------------------------------------------

物资回收
---------------------------------------------------------------------
