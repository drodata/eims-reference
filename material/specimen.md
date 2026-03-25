# 留样

不论是自产还是外购的核心物资，都应该养成留样的规矩，防止客户反馈问题时，厂里由于手上没有任何留样而陷入被动。

检测环节会生成唯一批次号，留样就用这个批号作为主键。**当前仅在采购环节采取留样**。

`unit.has_specimen` 列用来判断一个 purchase item 是否需要留样。对于需要留样的采购单，入库前会进行额外检查，确保留操作按照要求操作。

结构
---------------------------------------------------------------------------

### Specimen Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 和 Unit 共用主键
`branch_id`                         | int       | No   | 账套
`section_id`                        | int       | No   | 
`type`                              | int       | No   | Lookup 普通、共用和强制三种
`parent_id`                         | int       | Yes  | 类别是“共用”时必填
`quantity`                          | int       | No   | 留样数量.类别是“普通”时必填
`status`                            | int       | No   | 已创建(1)、已交付(3)、已签收（9）、已归档（14）

### 权限
Role/Permission Name    | Description   |  Roles   
------------------------|---------------|-----------------
`createSpecimen`        |               | qualityChecker
`operateSpecimen`       |               | qualityChecker, materialKeeper
`manageSpecimen`        |               | qualityChecker, materialKeeper

操作
---------------------------------------------------------------------------

### 新建
在 `detect_purchase_item` 表示建立 grid.

### 交付
`specimen/deliver`. 质检员点击，记录“交接”的过程。

- 更新状态为“已交付”；

### 签收
`specimen/store`. 仓管员点击

- 更新状态为“已签收”；

变更
--------------------------------------------------------------------------
- 2026-03-25 调整 `Schema`: 增加 `section_id` 列，制品2部的仓管只显示她部门的记录；
