# 采购

工作交接
---------------------------------------------------------------------------

hoardPurchaser 交接工作时执行 `./yii handover/hoard-purchaser id1 id2`. 其中 `id1` 是原负责人用户 ID, `id2` 是新负责人。

交接工作要点：

0. 查看原负责人是否有未完成评审的采购需求；
1. 更新所有未完成的 DemandItem 的 `bought_by` 值；
2. 更新所有未完成的 Purchase 的 `created_by`, `purchaser_id` 值. 后者会影响到签收操作；
