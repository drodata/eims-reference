# 首页

Change Logs
---------------------------------------------------------------------------

日期        | 大类              | 类别      | 动作 | 说明
------------|-------------------|-----------|------|-------------------
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
[console-monitor]: /console/monitor.md
[schema-bucket-selection-trace-snap]: /models/bucket.md#bucketselectiontracesnap-schema
[schema-mix-lapse]: /production/mix.md#mixlapse-schema
[action-purchase-item-build-detection]: /purchasing/purchase.md#purchase-item/build-detection
[topic-user-domain-filter]: /topics/user-domain-filter.md

[section-purchase-specimen]: /purchasing/purchase.md#留样
[section-purchase-inspection]: /purchasing/purchase.md#检测
[section-purchase-concession]: /purchasing/purchase.md#让步接收
[section-goods-grinding-paste-via]: /order/goods.md#研磨膏制作途径

