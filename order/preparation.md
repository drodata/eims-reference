# 
订单备货使用 `PurchasingPreparation` 模型承载。

PurchasingPreparation
---------------------------------------------------------------------------

状态常量 | 状态值 | 值改变场景
--------|----------|-------
`STATUS_CREATED` | 1 | 新建记录
`STATUS_CONFIRMED` | 2 | `purchasing_preparation/confirm`
`STATUS_INQUIRED` | 3 | `purchasing_preparation/inquire`
`STATUS_REFUSED` | 4 | 总经理审批不同意购买时 `opinion/create-plan`
`STATUS_COMPLETED` | 5 | 总经理审批同意购买时 `opinion/create-plan`

流程要点：

- 仓管员在订单取料页面申请备货。创建后点击“确认”，转交采购员询价（采购员微信会收到询价工作提醒）；
- 采购员询价后生成新的 `PlanItem` 记录，供接下来的新建采购计划使用；
- 总经理评审采购计划，同意后，采购员需手动生成需求明细；如果不同意，直接将状态设置为不同意，结束。

