# 购买清单

此清单都是经过总经理确认、批准购买的物品。

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

### 七种状态

状态常量                | 状态值    | 值改变场景
------------------------|-----------|-------
`STATUS_CREATED`        | 1         | 新建记录
`STATUS_CONFIRMED`      | 2         | 仓库确认
`STATUS_REVIEWED`       | 3         | 采购询价
`STATUS_DISAPPROVED`    | 4         | 总经理拒绝购买
`STATUS_APPROVED`       | 5         | 总经理同意购买
`STATUS_ENDED`          | 6         | 仓库生成需求明细
`STATUS_DISCARDED`      | 14        | 采购申请作废(客户备货), 采购拒绝备货(订单备货)

- 已作废(discarded):
    - 客户备货评审通过后，如遇其它原因不再采购，可以进行“作废”操作。该操作通过 Edition 承载，评审完成后将 PlanItem 状态知设置为“已作废”；
    - 订单备货可以通过“拒绝”操作将状态设置为“已作废”。作废后，仓库不需要删除备货记录，系统自动判定为无效记录；

创建时机
---------------------------------------------------------------------------

- PREPARATION: `purchasingpreparation/create` 同 `purchasing_preparation` 一起创建, 参见 `Preparation::build()`;

操作列表
---------------------------------------------------------------------------
### 生成需求明细
`plan-item/generate`. 用于手动生成需求清单关联的购买清单。适用于除普通采购需求外的其它所有类型。

Route                           |   名称    | 说明
--------------------------------|-----------|---------
`plan-item/delay`               |延期       | 
`plan-item/delete`              |删除       | 通过 `./yii pile write` 生成的记录，仓库核对后如过发现不符，可以删除；
`plan-item/inquire`             |询价       | 
`plan-item/cancel-inquire`      |撤销询价   | 这里使用了抽象的 `cancel` action, 携带一个 `$key` 参数标记撤销的操作类别。
`plan-item/deny`                |拒绝       |发生在仓库确认后或总经理拒绝购买后,拒绝后状态变为“已作废”

Change Logs
--------------------------------------------------------------------------
- 2023-10-24 `Enh` DetectRejectItem schema: xxx
