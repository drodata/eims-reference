# 首页

Change Logs
---------------------------------------------------------------------------

日期        | 大类              | 类别      | 动作 | 说明
------------|-------------------|-----------|------|-------------------
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
[console-monitor]: /console/monitor.md
[schema-bucket-selection-trace-snap]: /models/bucket.md#bucketselectiontracesnap-schema
[schema-mix-lapse]: /production/mix.md#mixlapse-schema
