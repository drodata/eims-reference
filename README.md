# 首页
[Summary](SUMMARY.md)

Change Logs
---------------------------------------------------------------------------
- 2024-07-01 `Enh` PurchaseItemFactor Schema, 增大 `base_d50` 和 `offset_d50` 精度各一位，解决类似 1.75 的中值无法填写的问题；
- 2024-06-22 `Enh` [DemandItem][demand] 和 [Demand][demand]: 增加`section_id`, 按照小部门生成对应记录；
- 2024-06-21 `Enh` [Review][review]: 增加`section_id`, 按照小部门生成台帐检查
- 2024-06-20 `Enh` [Requisition][requisition]: 增加`section_id`, 物资领用精确到以小部门为单位
- 2024-06-20 `Enh` [User][user]: 增加`user.section_id`, 同时解决消耗品领用和采购需求申请时申请部门不明确的问题；
- 2024-06-18 `Enh` 改进 [Transfer 物资借调][transfer]: 改成以部门 (Section)为单位. 同时停用早前的 Allotment 模型；
- 2024-06-13 `Enh` 扩展 [Withdrawal][withdrawal], 承载领料交付后重新退还仓库的情形；
- 2024-06-03 `Add` [物资调拨单 Allotment][allotment], 解决同一账套内、不同部门间物资的流动；
- 2024-05-27 `Enh` 扩展 Manufacture, 增加 `branch_id`, `section_id`, `type` 列，承载组团磨料生产单；
- 2024-05-25 `Add` [核算部门 Section][section], 区分制品2部和微粉部.
- 2024-05-23 `Enh` [采购需求明细 (DemandItem)支持询价 (补录单价)][section-demand-item-inquire].
- 2024-05-19 `Enh` [制品中心追溯码改为自动生成，不再手动录入][section-trace-usage]. 不再打印追溯码
- 2024-05-13 `Enh` [采购单和采购调配单增加评审环节][section-purchase-audit], hoardNewbie 新建的单子必须经 hoardPurchaser 评审；
- 2024-03-27 `Enh` [生产单增加变更操作][warehousing], 允许修改强度、厂家等信息；
- 2024-03-21 `Enh` [物资借调][transfer]. 启用评审、录入单价操作；
- 2024-03-15 `Add` 权限 `managePress` 和 `createPress`;
- 2024-03-12 `Enh` 评审通过的采购需求,在列入计划前，支持[作废操作][section-demand-discard];
- 2024-01-19 `Enh` 混料单增加[`need_press` 列控制压块可见性][section-mix-toggle-press],避免压块页面混料列表越来越长；
- 2024-01-19 `Add` [压块新增“作废”操作][section-press-discard], 应对录入错误的情况.装块操作类似；
- 2024-01-05 `Add` [hoard newbie 工作迁移操作][section-purchase-handover-hoard-newbie], 应对采购离职后的工作交接；
- 2024-01-03 `Add` [定向物资页面 (dedicated-material/index)][section-material-dedicated], 业务员和仓库能直观看到当前的定向物资；
- 2023-12-29 `Adj` Order: 取消[订单评审前的匹配备货限制][match-hoard], 不再强制匹配备货；
- 2023-12-22 `Enh` Purchase: 新增[采购明细具体数据要求 PurchaseItemFactor][schema-purchase-item-factor]. CG-6237 以后的采购单开始生效；
- 2023-12-22 `Bug` [采购单确认合格(`purchase-item/check`)出现脏数据][fault-trigger-not-in-transition]
- 2023-12-19 `Enh` [Exchange][exchange] 简化 ExchangeItem, 舍弃 `sku_id`; 改用通用 Pickup 承载取料过程；
- 2023-12-17 `Fix` [Outflow ][outflow] AB类客户流失生成逻辑, 超过 90 天没有大货交付需上报原因；
- 2023-12-15 `Enh` [GrindingWheelProduction ][gwp]: 增加 `is_oem` 列，承载代加工砂轮生产；
- 2023-12-14 `Feedback` 订单终止交付后余额数量未同步
  
  业务员反应 20985 订单出现终止交付后，订单关联的余额记录仍旧是终止前的金额。
  检查 `OrderEnd::apply()` 逻辑后确认没有问题。
  因此该订单可能是终止交付启用前的旧订单。保持关注；
- 2023-12-07 Add [AdminLTE 页面][topic-adminlte] `.table-detail` 全局类, 统一各种表单详情页的样式；
- 2023-11-30 Enh Coating: 和订单关联 [订单镀覆加工 Coating][coating]
- 2023-11-22 Add OemDeliveryRefuse: 新增[代加工退货][section-oem-delivery-refuse]

日期        | 大类              | 类别      | 动作 | 说明
------------|-------------------|-----------|------|-------------------
2023-11-20  | Hoard             | Schema    | 扩展 | 承载[公司A类物资备货][company-hoard]
2023-11-10  | Material          | Schema    | 改进 | [Material][material]增加 `cost` 和 `stored_at`
2023-11-07  | OemDelivery       | Logic     | 改进 | 不合格品入库前增加[让步接收评审][section-oem-delivery-concession]
2023-10-08  | BucketItem        | Schema    | 改进 | 领料申请支持[无需取料][section-bucket-item-toggle-pickness]
2023-09-20  | Purchase          | Logic     | 改进 | 检测项目可根据产品类别[自动填充][section-purchase-inspection-methods]
2023-09-18  | Billing           | Logic     | 调整 | 202308 账期开始的月结帐单改为[以订单交付为统计依据][section-customer-billing-modern-billing]
2023-08-25  | RBAC              | Logic     | 新增 | [通用 HolderRule][topic-rbac-holder-rule]
2023-09-06  | Bucket            | Schame    | 改进 | 新增 `branch_id` 承载超硬系统内原自产微粉领料申请记录 (Picking)
2023-09-01  | Sku               | Logic     | 改进 | 新建 SkuAdditionalProperty 接管[附加属性的设置][section-material-sku-additional-property]
2023-09-01  | Order             | Logic     | 调整 | 订单交付单出库前[强制检测][section-order-delivery-detection]
2023-08-25  | Reject            | Logic     | 调整 | [不合格退货][section-reject-concession]改用 DetectionConcession 承载
2023-08-23  | Goods             | Schema    | 新增 | [研磨膏制作途径][section-goods-grinding-paste-via]区分
2023-08-19  | Misc              | Filter    | 新增 | [用户作用域 Filter][topic-user-domain-filter]
2023-08-14  | Purchase          | Logic     | 新增 | [让步接收评审][section-purchase-concession]
2023-08-11  | Purchase          | Logic     | 调整 | 采购单检测支持[一键检测][section-purchase-inspection]
2023-08-10  | Bucket            | Logic     | 调整 | 结合剂单位调整为“千克”, 且不再在生产领料单中出现；
2023-08-09  | Specimen          | Schema    | 新增 | `type` 和 `parent_id`. 解决强制留样和共用留样的场景
2023-08-08  | Fixture           | Logic     | 调整 | 采购员允许提交物资登记，承载原来采购单中的免费样品部分
2023-08-05  | Specimen          | Model     | 新增 | 物资留样并应用在[采购检测环节][section-purchase-specimen]
2023-08-04  | Requisition       | Schema    | 新增 | `branch_id` 列
2023-08-01  | Review            | Model     | 新增 | [台帐一致性检查][generic-review]
2023-07-23  | PurchaseItem      | Action    | 新增 | [共享检测报告][action-purchase-item-build-detection]
2023-07-22  | Lapse             | Model     | 扩展 | [混料阶段数量异常上报][schema-mix-lapse]
2023-07-21  | Bucket            | Model     | 新增 | [数量变动日志][schema-bucket-selection-trace-snap]
2023-07-18  | Product           | Action    | 新增 | `product/build-factory`, `product/factory` 新增和管理厂区标准粒度
2023-07-18  | MixItem           | Action    | 新增 | `mix-item/append`向已存在的混料单追加明细；
2023-07-14  | Balance           | Action    | 新增 | `balance/correct-amount` 纠正紊乱的余额数量；
2023-07-14  | Monitor(C)        |           | 新增 | [客户余额一致性监测][console-monitor]
2023-07-04  | Withdrawal        |           | 新增 | [通用取料退回][generic-withdrawal]并应用到复合片加工退回
2023-06-28  | Requisition       |           | 新增 | 消耗品领用模型,承载低值易耗品和劳保物资的领用
2023-06-21  | BucketItem        | Schema    | 新增 | `measurement_unit` 单位列
2023-06-14  | Bucket            | Action    | 弃用 | `bucket/confirm-delivery`, BucketDelivery 增加签收操作后，此操作已意义；
2023-06-14  | BucketItem        | Action    | 新增 | `bucket-item/tweak-quantity`, 评审后可以微调数量;

[generic-withdrawal]: /models/withdrawal.md
[withdrawal]: /models/withdrawal.md
[requisition]: /models/Requisition.md
[user]: /models/user.md
[generic-review]: /models/review.md
[review]: /models/review.md
[section]: /models/section.md
[material]: /material/material.md
[transfer]: /material/transfer.md
[allotment]: /material/allotment.md
[coating]: /order/coating.md
[match-hoard]: /order/match-hoard.md
[console-monitor]: /console/monitor.md
[company-hoard]: /purchasing/hoard.md
[demand]: /purchasing/demand.md
[demand-item]: /purchasing/demand-item.md
[exchange]: /purchasing/exchange.md
[gwp]: /production/grinding-wheel-production.md
[warehousing]: /production/warehousing.md
[deal]: /sale/deal.md
[outflow]: /customer/outflow.md

[topic-user-domain-filter]: /topics/user-domain-filter.md
[topic-rbac-holder-rule]: /topics/rbac-holder-rule.md
[topic-adminlte]: /topics/adminlte.md

[section-purchase-handover-hoard-newbie]: /purchasing/README.md#hoard-newbie
[section-purchase-specimen]: /purchasing/purchase.md#留样
[section-purchase-inspection]: /purchasing/purchase.md#检测
[section-purchase-inspection-methods]: /purchasing/purchase.md#检测项目
[section-purchase-concession]: /purchasing/purchase.md#让步接收
[section-purchase-audit]: /purchasing/purchase.md#评审
[section-demand-discard]: /purchasing/purchase.md#作废
[section-demand-item-inquire]: /purchasing/demand-item.md#询价
[section-goods-grinding-paste-via]: /order/goods.md#研磨膏制作途径
[section-reject-concession]: /order/reject.md#处理不合格品
[section-order-delivery-detection]: /order/delivery.md#检测
[section-material-sku-additional-property]: /material/sku.md#附加属性
[section-customer-billing-modern-billing]: /customer/billing.md#modern-billing
[section-bucket-item-toggle-pickness]: /models/bucket.md#无需取料
[section-trace-usage]: /models/trace.md#使用
[section-oem-delivery-concession]: /purchasing/oem.md#处理不合格品
[section-oem-delivery-refuse]: /purchasing/oem.md#退货
[section-material-dedicated]: /material/material.md#定向物资
[section-press-discard]: /production/press.md#作废
[section-mix-toggle-press]: /production/mix.md#控制压块可见性

[action-purchase-item-build-detection]: /purchasing/purchase.md#purchase-item/build-detection

[schema-bucket-selection-trace-snap]: /models/bucket.md#bucketselectiontracesnap-schema
[schema-mix-lapse]: /production/mix.md#mixlapse-schema
[schema-purchase-item-factor]: /purchasing/purchase.md#schema-purchaseitemfactor

[fault-trigger-not-in-transition]: /faults/trigger-not-in-transition.md
