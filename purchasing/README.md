# 采购

角色权限
---------------------------------------------------------------------------
- 角色: hoardNewbie, hoardPurchaser, pilePurchaser, purchaseDirector 等
- 权限： handlePurchasing, handleHoard, handleSeller 等
    - `viewPurchasePrice`: 查看单价: `saleDirector`, `finance`, `handlePurchasing`

要点如下：

- handleSeller 关联供应商新建、删除等相关操作。只有 pilePurchase 和 hoardPurchaser 拥有该权限；
- hoardNewbie 和 hoardPurchaser 允许同时存在,但 hoardPurchaser **只能有一个(采购主帐号)**；前者表示刚入职的采购员，后者是正式的。区别：前者只能看到自己提交的采购单、前者看不到供应商信息；
- purchaseDirector 的作用：对采购单的任何修改(Edition 承载)需要评审时，需要此角色参与；

purchaser 和 temporaryPurchaser 逐步弃用。

### hoardNewbie 和 hoardPurchaser 的关系

purchaseNewbie 提交的以下三种评审，需要先经过 hoardPurchase:

- 新建采购计划
- 新建采购单付款申请
- 新建供应商付款申请

工作交接
---------------------------------------------------------------------------

### Hoard Newbie
见习采购离职后，先把帐号下未处理的 notifications 处理完，然后执行 `./yii handover/hoard-newbie id1 id2` 即可将其未完成的工作合并的采购主管帐号下。此操作依次更新以下内容：

0. 已评审但未列入计划的采购需求的购买负责人；
1. 未完成的购买清单的购买负责人；
2. 未完成的采购单：修改 `created_by` 和 `keeped_by` (影响后续的签收操作)；
3. 未完成的物资登记单：修改 `created_by`;

### Hoard Purchaser
hoardPurchaser 交接工作时执行 `./yii handover/hoard-purchaser id1 id2`. 其中 `id1` 是原负责人用户 ID, `id2` 是新负责人。

交接工作要点：

0. 查看原负责人是否有未完成评审的采购需求；
1. 更新所有未完成的 DemandItem 的 `bought_by` 值；
2. 更新所有未完成的 Purchase 的 `created_by`, `keeped_by` 值. 后者会影响到签收操作；
3. 更新 `seller.owned_by` 为新负责人
