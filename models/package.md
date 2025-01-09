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
`type`                              | string    | No   | 'warehousing', 'manufacture', 'bundle'
`box`                               | int       | No   | Lookup 类型：桶(1)、袋子(2)
`quantity`                          | int       | Yes  | type 为 'bundle' 时不需要
`position`                          | int       | Yes  | 序号
`status`                            | int       | Yes  | 预留
`parent_id`                         | int       | Yes  | 父 ID
`warehousing_id`                    | int       | Yes  | 关联生产单
`manufacture_id`                    | int       | Yes  | 关联生产单
`bundle_id`                         | int       | Yes  | 关联生产单

`sku_id`                            | int       | No   | 
`container_id`                      | int       | No   | 容器编号。自产
 * @property integer $id
 * @property string $type
 * @property integer $box
 * @property integer $quantity
 * @property integer $position
 * @property integer $status
 * @property integer $parent_id
 * @property integer $warehousing_id
 * @property integer $manufacture_id
 * @property integer $bundle_id
 * @property integer $warehouse_id
 * @property integer $unit_id
