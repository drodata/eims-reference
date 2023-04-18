# 备货
订单备货使用 `PurchasingPreparation` 模型承载。

状态常量 | 状态值 | 值改变场景
--------|----------|-------
`STATUS_CREATED` | 1 | 新建记录
`STATUS_CONFIRMED` | 2 | `purchasing_preparation/confirm`
`STATUS_INQUIRED` | 3 | `purchasing_preparation/inquire`
`STATUS_REFUSED` | 4 | 总经理审批不同意购买时 `opinion/create-plan`
`STATUS_COMPLETED` | 5 | 总经理审批同意购买时 `opinion/create-plan`

