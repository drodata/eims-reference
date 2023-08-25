# 退货

状态常量                | 状态值 | 值改变场景
------------------------|--------|------------
`STATUS_CREATED`        |   1    | 
`STATUS_RECEIVED`       |   2    | 签收
`STATUS_INSPECTED`      |   4    | 检测
`STATUS_DECIDED`        |   6    | 处理不合格品
`STATUS_STORED`         |   5    | 入库

检测
---------------------------------------------------------------------------
### DetectRejectItem Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`goods_id`                          | int       | No   | 退货明细 id
`detection_id`                      | int       | No   | 检测编号
`decision`                          | int       | Yes  | Lookup: 通过(1), 退回(2)、让步(3)
`refund` `                          | int       | Yes  | 退款金额

### 处理不合格品

检测确认完成后，对于合格品，系统直接将 `detect_reject_item.decision` 值设置为”通过“；如合格品则由业务员处理。两种处理方式：

- 退回客户 (`detect-reject-item/refuse`)：预留，基本不用；
- 入库 (`detect-reject-item/concede`)：同时创建关联的 DetectionConcession 记录，完成评审后，仓库可以入库；

不合格品入库评审流程：业务员→销售经理 → 生产经理 → 总经理

退回客户和让步接收终审后均会触发 `EVENT_DECISION_CONFIRMED`, 该事件绑定的 handler 会更新退货单状态为“已处理”，此状态是入库的前置条件。

Change Logs
---------------------------------------------------------------------------
- 2023-08-25 `Enh` DetectRejectItem schema: 新增 `dicision` 和 `refund`. 舍弃的 `UnqualifiedReject` 模型, 并入 `DetectionConcession` 承载不合格品入库；
