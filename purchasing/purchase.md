# 采购单

Schema
---------------------------------------------------------------------------
### PurchaseItem Schema

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`refuse_id`                         | int       | Yes  | 退货编号
`purchase_id`                       | int       | Yes  | 采购单号。退货也记录
`type`                              | int       | No   | 类型 Lookup
`sku_id`                            | int       | No   | 
`inspection_methods`                | int       | Yes  | 
`was_inspected`                     | int       | No   | 默认是 0,
`demand_item_id`                    | int       | Yes  | 
`refuse_detection_id`               | int       | Yes  | 退货单是必填
`action`                            | int       | No   | 订货、退货、换货
`price`                             | int       | Yes  | 
`quantity`                          | int       | No   | 

拆分
---------------------------------------------------------------------------

换货也可以拆分, 换货本质上和采购单一样。

检测
---------------------------------------------------------------------------
采购单的检测以采购明细为单位，签收以后进行。根据 `purchase_item.inspection_methods` 的值，分为两种情况：

1. 值为 null: 普通物资，采购员可以快速检验（`purchase-item/check`）完成检测；
2. 值不是 null: 需要质检员录入检测结果. 录入完成后，通过 `purchase-item/confirm-inspection` 完成检测；

不管那种情况, 都会将 `purchase_item.was_inspected` 设置为 0, 标记检测完成，同时触发 `PurchaseItem::EVENT_INSPECTION_CONFIRMED` 事件，进而动态判断采购单状态是否可以设置为“已检测”。

让步接收
---------------------------------------------------------------------------
质检检测发现不合格品后，采购员应对不合格品做出决定。如果选择”让步接收“，则需要进行评审，评审过程由[通用不合格品让步接收][generic-detection-concession]模型承载。本质上就是创建一条 `DetectionConcession` 记录.

留样
---------------------------------------------------------------------------

采购单中的核心物资必须留一定数量的样品。留够三年，之后可以丢掉。留样分为以下三种类型（使用 `specimen.type` 区分）：

1. 普通(common).这是最理想也是最简单的情况：检测的每个批次都附带的有样品，录入检测数量即可；
2. 共用(shared)：由于采购清单的限制，采购单经常出现这样一种情况：系统内显示的批次不同，但实际上是同一包料。这时只需要创建一个“普通”类型的留样记录，其余批次和它共用留样即可；
3. 强制(compulsory)：这是确保留样规则生效的最后一种情况，防止供应商不附带样品，此时将直接从大货中扣除样品；

权限表

Role/Permission Name    | Parent        
------------------------|---------------
`createSpecimen`        | `qualityChecker`
`viewSpecimen`          | `createSpecimen`, `warehouseKeeper', `gwDirecto`, `productionDirector`, `saleDirecto` 

### 启用和撤销

- `unit/enable-specimen`
- `unit/revoke-specimen` (Edition 承载, 评审：质检员 → 采购员 → 生产经理)

操作列表
---------------------------------------------------------------------------

### purchase-item/inspection-portal
质检员的检测入口页面。

### purchase-item/confirm-inspection

### purchase-item/check
采购员一键检测

### purchase-item/build-detection

另一种检测方式，省去重复录入上传报告，实现检测报告的共享使用。共享意味着 inspection 和 detection 可能存在一对多的可能，这就需要在 `detection/delete` 删除关联 inspection 前做一个判断：如果一个 inspection 还被其它 detection 使用，则仅删除关联表，否则连同 inspection 一并删除。详见 Issue 235.

Change Logs
---------------------------------------------------------------------------
日期        | 类别      | 动作 | 说明
------------|-----------|------|-------------------
2023-08-15  | Action    | 新增 | 让步接收申请评审
2023-08-11  | Scheme    | 新增 |`purchase_item.was_inspected`;

[generic-detection-concession]: /models/detection-concession.md
