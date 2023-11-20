# 首页
[Summary](SUMMARY.md)

Change Logs
---------------------------------------------------------------------------

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
[generic-review]: /models/review.md
[material]: /material/material.md
[console-monitor]: /console/monitor.md
[company-hoard]: /purchasing/hoard.md
[schema-bucket-selection-trace-snap]: /models/bucket.md#bucketselectiontracesnap-schema
[schema-mix-lapse]: /production/mix.md#mixlapse-schema
[action-purchase-item-build-detection]: /purchasing/purchase.md#purchase-item/build-detection

[topic-user-domain-filter]: /topics/user-domain-filter.md
[topic-rbac-holder-rule]: /topics/rbac-holder-rule.md

[section-purchase-specimen]: /purchasing/purchase.md#留样
[section-purchase-inspection]: /purchasing/purchase.md#检测
[section-purchase-inspection-methods]: /purchasing/purchase.md#检测项目
[section-purchase-concession]: /purchasing/purchase.md#让步接收
[section-goods-grinding-paste-via]: /order/goods.md#研磨膏制作途径
[section-reject-concession]: /order/reject.md#处理不合格品
[section-order-delivery-detection]: /order/delivery.md#检测
[section-material-sku-additional-property]: /material/sku.md#附加属性
[section-customer-billing-modern-billing]: /customer/billing.md#modern-billing
[section-bucket-item-toggle-pickness]: /models/bucket.md#无需取料
[section-oem-delivery-concession]: /purchasing/oem.md#处理不合格品
