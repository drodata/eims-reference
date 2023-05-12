# PlanItem

四种类型：

TYPE | 说明 | 创建时机
--------|----------|-------
DEMAND | 旧版采购需求 | Demand 评审评审同意后
PREPARATION | 订单备货 | 跟随 PurchasingPreparation 创建，`Preparation::build()`
PILE | 易耗品提前备货 | 跟随 Pile 创建，`./yii pile write`
HOARD | 客户备货 | 跟随 Hoard 创建，`micro-diamond-hoard/create` 等
