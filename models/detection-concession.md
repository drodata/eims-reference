# 不合格品接收
质检确认检测结果后，不合格批次禁止入库。如遇特殊情况，相关人员需提交不合格品入库申请, 该申请由 `detection_concession` 表承载。

DetectionConcession 之所以和 Detection 共用主键, 是因为所有的让步接收都是建立在某个检测记录上，这么设计可以让“让步接收”变得通用——不管采购单还是二次加工单，只要不合格，入库前都得进行评审。区别在于评审人不同。

目前已经部署的地方有：

- 采购单检测；
- 退货单检测；

###更加通用的 Concession
这个 DetectionConcession 可以抽象出更加通用的 Concession, 本来打算用在 Selection 取料(防止外购料高买低卖)上，后来取消。以后出现类似需求时再调整。

### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 和 Detection 共用主键
`branch_id`                         | int       | No   | 账套
`reason`                            | string    | No   | 理由
`status`                            | int       | No   | 已创建(1)、已同意(9)、不同意 (14)
