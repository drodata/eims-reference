# 分装

结构
---------------------------------------------------------------------
### 权限
Role/Permission Name                | Description           |  Parents
------------------------------------|-----------------------|-----------------
`handlePackage`                 |处理分装操作权限     | warehouseKeeper, gaWarehouseKeeper

### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`type`                              | string    | No   | 'warehousing', 'manufacture', 'detection', 'bundle'
`box`                               | int       | No   | Lookup 类型：桶(1)、袋子(2)
`quantity`                          | int       | Yes  | type 为 'bundle' 时不需要
`position`                          | int       | Yes  | 序号
`status`                            | int       | Yes  | 预留
`parent_id`                         | int       | Yes  | 父 ID
`warehousing_id`                    | int       | Yes  | 关联生产单
`manufacture_id`                    | int       | Yes  | 关联生产单
`detection_id`                      | int       | Yes  | 关联 detection
`bundle_id`                         | int       | Yes  | 关联生产单
`warehouse_id`                      | int       | Yes  | 'bundle' type 时必须
`unit_id`                           | int       | Yes  | 'bundle' type 时必须
