# 不合格品让步接收
质检确认检测结果后，不合格批次禁止入库。如遇特殊情况，相关人员需提交“不合格品让步接收申请”, 该申请由 `detection_concession` 表承载。

DetectionConcession 之所以和 Detection 共用主键, 是因为所有的让步接收都是建立在某个检测记录上，这么设计可以让“让步接收”变得通用——不管采购单还是二次加工单，只要不合格，入库前都得进行评审。区别在于评审人不同。

最先应用在采购单让步接收。

### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 和 Detection 共用主键
`branch_id`                         | int       | No   | 账套
`reason`                            | string    | No   | 理由
`status`                            | int       | No   | 已创建(1)、已同意(9)、不同意 (14)
