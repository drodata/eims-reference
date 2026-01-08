# 检验报告

新增步骤
---------------------------------------------------------------------------
- 表格: `Inspection Xxx`.
- alter column `purchase_item.inspection_methods`
- InspectionXxx
    - `getInspection()`
- Inspection
    - `getXxx()`
    - `hasXxx()` 是否含有检测项目的判断方法
    - `deleteInspectionXxx()`
    - `actionLink()` 增加检测按钮
    - `validateMethods()`
- Media (上传附件时)
    - 新增对应的 CATEGORY
- MediaForm (上传附件时)
- InspectionWeidhtLossRatio Controller
    - `use backend\models\MediaForm` (上传图片时)
    - `actionCreate()`: 更新跳转链接
    - `actionUpdate()`: 更新跳转链接
- `inspection/_field-purchase`: 增加入口链接和结果展示
- `inspection/_detail-table` 新增项目表格详情
外观尺寸检测
---------------------------------------------------------------------------
### InspectionExterior Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`inspection_id`                     | int       | No   | 
`is_consistent`                     | int       | No   | 描述是否一致
`is_intact`                         | int       | No   | 包装完整性

图像检测
---------------------------------------------------------------------------
整合之前的显微镜和电镜检测。

### InspectionGraphics Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`inspection_id`                     | int       | No   | 
`media_id`                          | int       | No   | 
`device`                            | int       | No   | Lookup `graphics-device`
`shape`                             | int       | Yes  | Lookup `powder-shape`
`mignification`                     | int       | Yes  | Lookup `sem-mignification`

失重率
---------------------------------------------------------------------------

### InspectionWeightLossRatio Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`inspection_id`                     | int       | No   | 
`value`                             | string    | No   | 

耐火度
---------------------------------------------------------------------------

### InspectionRefractoriness Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`inspection_id`                     | int       | No   | 
`value`                             | string    | No   | 
`media_id`                          | int       | No   | 图片（单张）

操作
--------------------------------------------------------------------------
### 修改检测项目
`inspection/update-methods`. 通常情况下，模型自身会附带 `inspection_methods` 列，
有的简单模型中，没必要增加此列，因此在这里进行项目的修改，包括：

- Manufacture 组团磨料成品检验；
- Rename 物资改名检测阶段；
- Reject 订单退货检测阶段（退货单和订单公用 goods 表，没有对应列）；

限制条件：**操作前应确保没有上传任何检测项目的数据**。

### 追加检测项目

`inspection/append-methods`. 有的物资在检测完成后会增加检测项目，
让检测报告更加完整，方便查看。**质检员**通过此操作完成。

本质：更改 `inspection.methods` 列值。

变更
--------------------------------------------------------------------------
- 2026-01-04 新增追加检测项目(append-methods)操作；
