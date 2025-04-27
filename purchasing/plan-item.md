# 需求清单

结构
---------------------------------------------------------------------------

### Schema
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
PREPARATION | 订单备货 | 编号 `BH-XXX`. 跟随 PurchasingPreparation 创建，`Preparation::build()`
PILE | 易耗品提前备货 | 跟随 Pile 创建，`./yii pile write`
HOARD | 客户备货 | 跟随 Hoard 创建，`micro-diamond-hoard/create` 等

### 七种状态

状态常量                | 状态          | 值改变场景
------------------------|---------------|-------
`STATUS_CREATED`        | 已创建(1)     | 新建记录
`STATUS_CONFIRMED`      | 已确认(2)     | 仓库确认
`STATUS_REVIEWED`       | 已询价(3)     | 采购询价
`STATUS_DISAPPROVED`    | 未批准(4)     | 总经理拒绝购买
`STATUS_APPROVED`       | 已批准(5)     | 总经理同意购买
`STATUS_ENDED`          | 已完成(6)     | 仓库生成需求明细
`STATUS_DISCARDED`      | 已作废(14)    | 采购申请作废(客户备货), 采购拒绝备货(订单备货)

- 已作废(discarded):
    - 客户备货评审通过后，如遇其它原因不再采购，可以进行“作废”操作。该操作通过 Edition 承载，评审完成后将 PlanItem 状态知设置为“已作废”；
    - 订单备货可以通过“拒绝”操作将状态设置为“已作废”。作废后，仓库不需要删除备货记录，系统自动判定为无效记录；

创建时机
---------------------------------------------------------------------------

- PREPARATION: `purchasingpreparation/create` 同 `purchasing_preparation` 一起创建, 参见 `Preparation::build()`;

操作
---------------------------------------------------------------------------
### 确认
`plan-item/confirm`. 更新状态，并向采购员发送微信提醒. 详见 `PlanItem::confirm()`.

### 拒绝
`plan-item/deny`. 采购员有权利拒绝仓库提交的备货申请。更新单据状态为“已作废”. 作废的单据无法修改，
如果需要再次备货, 仓库提交一条新纪录即可。

### 作废
`plan-item/discard`. 

- pile: 在仓库确认后、创建采购计划前的这段时间，可以作废。作废需要仓管员评审；
  采购计划评审通过后将无法作废，此时采购员可以先生成购买清单，而后通过"标记完成"操作实现作废；
- hoard: 评审通过后可以作废；
- demand: 无需作废，在购买清单上进行“标记完成”操作；
- preparation: 无需作废，可直接”拒绝“ (deny)；

### 询价
`plan-item/inquire`. 采购员填写询价内容后更新状态为“已询价”。
PlanItem 中有专门的 `SCENARIO_INQUIRE` 控制必填项。

对于采购备货询价时，系统会和关联的订单销售价进行比对，避免出现采购价高于销售价的情况。

接下来，采购通过创建采购计划让总经理审批。

总经理通过 `plan/decide` 更新需求清单状态：
- 同意：更新状态为 “已批准”
- 不同意：更新状态为 “未批准”. 未批准的单字到此结束，不再显示在首页上。
### 撤销询价
`plan-item/cancel`. 询价的逆操作。将状态还原至“已确认”。

### 生成购买清单

`plan-item/generate`. 用于手动生成需求清单关联的购买清单(demand item)。适用于除普通采购需求外的其它所有类型。

生成后，更新状态为“已完成”.至此，PlanItem 生命周期结束，转向自动生成的[购买清单][demand-item] 上继续。

Route                           |   名称    | 说明
--------------------------------|-----------|---------
`plan-item/delay`               |延期       | 
`plan-item/delete`              |删除       | 通过 `./yii pile write` 生成的记录，仓库核对后如过发现不符，可以删除；
`plan-item/inquire`             |询价       | 
`plan-item/cancel-inquire`      |撤销询价   | 这里使用了抽象的 `cancel` action, 携带一个 `$key` 参数标记撤销的操作类别。
`plan-item/deny`                |拒绝       |发生在仓库确认后或总经理拒绝购买后,拒绝后状态变为“已作废”

Change Logs
--------------------------------------------------------------------------
- 2025-04-25 `Enh` 作废操作。pile 类型在新建计划前可以作废；
- 2023-10-24 `Enh` DetectRejectItem schema: xxx

[demand-item]: /purchasing/demand-item.md
