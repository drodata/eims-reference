# 工作逾期上报 OverdueReport
---------------------------------------------------------------------------

下辖具体模型包括：OverdueOrder


Column | Type | Note
--------|----------|-------
`id` | int | unit id
`type` | int | 1: 订单未交付, 2:需求明细未按时入库
`status` | int | 1: created, 2: completed

状态常量 | 状态值 | 值改变场景
--------|----------|-------
`STATUS_CREATED` | 1 | 新建记录


