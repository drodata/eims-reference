# 采购清单

Schema
---------------------------------------------------------------------------
Column                      | Type | Note
----------------------------|----------|-------
`id`                        | bigint | 
`branch_id`                 | int |
`type`                      | boolean |
`plan_id`                   | bigint |
`supplier`                  | string |
`price`                     | decimal |
`cycle`                     | integer |
`is_generated`              | boolean |
`status`                    | int |
`demand_id`                 | int |
`purchasing_preparation_id` | int |
`pile_id`                   | int |
`hoard_id`                  | int |

### 四种类型

TYPE | 说明 | 创建时机
--------|----------|-------
DEMAND | 旧版采购需求 | Demand 评审评审同意后
PREPARATION | 订单备货 | 跟随 PurchasingPreparation 创建，`Preparation::build()`
PILE | 易耗品提前备货 | 跟随 Pile 创建，`./yii pile write`
HOARD | 客户备货 | 跟随 Hoard 创建，`micro-diamond-hoard/create` 等

### 六种状态

状态常量                | 状态值    | 值改变场景
------------------------|-----------|-------
`STATUS_CREATED`        | 1         | 新建记录
`STATUS_CONFIRMED`      | 2         | 仓库确认
`STATUS_REVIEWED`       | 3         | 采购询价
`STATUS_DISAPPROVED`    | 4         | 总经理拒绝购买
`STATUS_APPROVED`       | 5         | 总经理同意购买
`STATUS_ENDED`          | 6         | 仓库生成需求明细

创建时机
---------------------------------------------------------------------------

- PREPARATION: `purchasingpreparation/create` 同 `purchasing_preparation` 一起创建, 参见 `Preparation::build()`;

变更交货期
---------------------------------------------------------------------------

`demand-item/delay`.

操作列表
---------------------------------------------------------------------------

- `inquire`: 询价
- `cancel-inquire`: 撤销询价。这里使用了抽象的 `cancel` action, 携带一个 `$key` 参数标记撤销的操作类别。
