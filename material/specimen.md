# 留样

不论是自产还是外购的核心物资，都应该养成留样的规矩，防止客户反馈问题时，厂里由于手上没有任何留样而陷入被动。

检测环节会生成唯一批次号，留样就用这个批号作为主键。当前仅在采购环节采取留样。

`unit.has_specimen` 列用来判断一个 purchase item 是否需要留样。对于需要留样的采购单，入库前会进行额外检查，确保留操作按照要求操作。

结构
---------------------------------------------------------------------------

### Specimen Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 和 Unit 共用主键
`branch_id`                         | int       | No   | 账套
`type`                              | int       | No   | Lookup 普通、共用和强制三种
`parent_id`                         | int       | Yes  | 类别是“共用”时必填
`quantity`                          | int       | No   | 留样数量.类别是“普通”时必填
`status`                            | int       | No   | 已创建(1)、已交付(3)、已签收（9）、已归档（14）

### 权限
Role/Permission Name    | Description   |  Parents
------------------------|---------------|-----------------
`viewSpecimen`          |               | `createSpecimen`, materialKeeper, productionDirector, saleDirector, gwDirector
`createSpecimen`        |               | qualityChecker

操作
---------------------------------------------------------------------------
Route                           |   名称    | 说明
--------------------------------|-----------|---------
`specimen/create`               | 新建      | 检测结果确认后由质检员新建
`specimen/deliver`              | 交付      | 质检发起，记录质检和仓库交接的过程
`specimen/store`                | 签收      | 仓库签收
