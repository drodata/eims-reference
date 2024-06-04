# Manufacture 通用生产单

目前，组团磨料和 CBN 微粉使用此模型，通过 `type` 列区分。

结构
---------------------------------------------------------------------
### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`type`                              | string    | No   | 'cbn', 'ga'
`branch_id`                         | int       | No   | 账套
`section_id`                        | int       | No   | 所属部门
`sku_id`                            | int       | No   | 
`container_id`                      | int       | No   | 容器编号。自产
`quantity`                          | int       | No   | 生产数量。入库时由仓库确定
`status`                            | int       | No   | 已创建、生产中、已入库

### status
Code                    | Value  | Note
------------------------|--------|------------
PREPARING               |   1    | 
DELIVERING              |   2    | 
COMPLETED               |   3    | 
TERMINATED              |   14   | 终止交付

变更日志
--------------------------------------------------------------------------
- 2024-05-27 `Enh` 增加 `branch_id`, `section_id`, `type`. 供制品2部使用
