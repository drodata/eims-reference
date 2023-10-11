# 采购

角色权限
---------------------------------------------------------------------------
- 角色: hoardNewbie, hoardPurchaser, pilePurchaser, purchaseDirector 等
- 权限： handlePurchasing, handleHoard 等

要点如下：

- hoardNewbie 和 hoardPurchaser 允许同时存在,但 hoardPurchaser **只能有一个(采购主帐号)**；前者表示刚入职的采购员，后者是正式的。区别：前者只能看到自己提交的采购单、前者看不到供应商信息；

purchaser 和 temporaryPurchaser 已弃用。

工作交接
---------------------------------------------------------------------------

hoardPurchaser 交接工作时执行 `./yii handover/hoard-purchaser id1 id2`. 其中 `id1` 是原负责人用户 ID, `id2` 是新负责人。

交接工作要点：

0. 查看原负责人是否有未完成评审的采购需求；
1. 更新所有未完成的 DemandItem 的 `bought_by` 值；
2. 更新所有未完成的 Purchase 的 `created_by`, `keeped_by` 值. 后者会影响到签收操作；
3. 更新 `seller.owned_by` 为新负责人
