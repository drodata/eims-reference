# Manufacture 通用生产单

目前，组团磨料和 CBN 微粉使用此模型，通过 `type` 列区分。

结构
---------------------------------------------------------------------
### 权限
Role/Permission Name                | Description           |  Parents
------------------------------------|-----------------------|-----------------
`manageManufacture`                 |管理生产单基础权限     | manageCbnManufacture, manageGroupedAbrasiveManufacture
`manageCbnManufacture`              |管理 CBN 生产单        | qualityChecker, productionDirector, saleDirector, warehouseKeeper
`manageGroupedAbrasiveManufacture`  |管理组团磨料生产单     | qualityChecker, productionDirector, saleDirector, gaWarehouseKeeper

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

四种状态:

Code                    | Value  | Note
------------------------|--------|------------
CREATED                 |   1    | 
PROCESSING              |   3    | 
COMPLETED               |   5    | 
DISCARDED               |  14    | 

### ManufactureProcessingInspection Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 和 manufacture 公用主键
`inspection_id`                     | int       | Yes  | 过程检验报告
`sender`                            | boolean   | Yes  | 'cbn', 'ga'
`status`                            | int       | No   | 已创建、已检测、已通过

状态表：

Code                    | Value  | Note
------------------------|--------|------------
CREATED                 |   1    | 
INSPECTED               |   3    | 
QUALIFIED               |   5    | 


操作
--------------------------------------------------------------------------
### 新建
质检创建。Manufacture 保存后，内部会同时初始化关联的 ManufactureProcessingInspection 
记录(`Manufacture::initializeProcessingInspection()`).
### 过程检验
`manufacture-processing-inspection/inspect`. 保存后：

- 更新状态为 INSPECTED (已检测);
- 更新关联生产单状态为 PROCESSING (生产中);

### 准予合格
`manufacture-processing-inspection/qualify`. 生产主管批准开始生产，该操作：
- 更新状态为 QUALIFIED (已通过);
- 初始化成品检验记录 (通过 DetectFinishedManufacture 关联表连接 Inspection);
### 作废
`manufacture/discard`. 过程检验通过后，生产单将无法删除。
在入库前这段时间，若出现特殊情况。质检可以申请作废。

> 评审顺序：质检员 → 仓管员 → 生产主管。

### 确认检测结果
`manufacture/confirm-finished-inspection`. 在录入检测结果后，质检员可以确认检测结果。
### 分装
`manufacture/package-portal`. 仓库完成标签打印的准备工作。
### 入库
`manufacture/store`

变更日志
--------------------------------------------------------------------------
- 2025-01-21 `Add` 作废操作
- 2024-12-28 `Add` 分装操作, 组团磨料使用;
- 2024-05-27 `Enh` 增加 `branch_id`, `section_id`, `type`. 供制品2部使用
