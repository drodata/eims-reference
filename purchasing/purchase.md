# 采购单

结构
---------------------------------------------------------------------------
### PurchaseItem Schema

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   | 账套
`section_id`                        | int       | No   | 所属部门
`refuse_id`                         | int       | Yes  | 退货编号
`purchase_id`                       | int       | Yes  | 采购单号。退货也记录
`type`                              | int       | No   | 类型 Lookup
`sku_id`                            | int       | No   | 
`inspection_methods`                | int       | Yes  | 
`was_inspected`                     | int       | No   | 默认是 0,
`demand_item_id`                    | int       | Yes  | 
`refuse_detection_id`               | int       | Yes  | 退货单是必填
`action`                            | int       | No   | 订货、退货、换货
`price`                             | int       | Yes  | 
`quantity`                          | int       | No   | 

- `section_id`: 采购明细内关联的需求单对应的申请部门，必须和采购单的所属部门一致；

### DetectPurchaseItem Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`purchase_item_id`                  | int       | No   | 
`detection_id`                      | int       | No   | 
`opinion`                           | int       | Yes  | 处理意见 Lookup `detect-purchase-item-opinion`

处理意见：

Code    | Name         | Note
--------|--------------|-------
1       | PASS         | 合格。`confirmInspection()` 内赋值
2       | REFUSE       |
3       | REPLACE      |
4       | CONCEDE      |
5       | DISMISS      |

### Schema PurchaseItemFactor

类似微粉这些特定的商品，需要搜集更精确的数据要求，而不是简单地写在备注栏内。单独创建一个表进行扩展。

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | `purchase_item.id` 共用主键
`type`                              | bool      | No   | 类型 Lookup
`base_d50`                          | decimal   | Yes  | decimal(5,2)
`offset_d50`                        | decimal   | Yes  | decimal(4,3)
`shape`                             | bool      | Yes  |
`shop_name`                         | string    | Yes  |
`note`                              | string    | Yes  | reserved

- **type**: 共用 `spu.type`. 指是 0 代表是店铺，此时 `shop_name` 必填；
    - `Spu::TYPE_MICRO_DIAMOND`： `base_d50`, `offset_d50` 和 `shape` 必填；
    - `Spu::TYPE_DIAMOND`： `shape` 必填；
    - `Spu::TYPE_RVD`： `shape` 必填；
    - `Spu::TYPE_ZXL`： `shape` 必填；

其它要点：

- 对采购单创建、修改、删除、拆分和退换货都有影响；
- 店铺名称的搜集依赖于供应商名称（必须是“实体店铺”）, 因此选择商品时要求供应商先得选好；
- 退货明细不需要；

新建
---------------------------------------------------------------------------

### 检测项目
如果检测项目为空, 签收后由采购员完成检测；反之由质检员完成检测.

核心商品的检测项目也可以为空，此时将不再需要留样（生成的批次记录中 `has_specimen` 值为 0），对于原包装核心商品可以采用此渠道。

检测项目具有自动填充功能，在 `Lookup::spuDefaultInspectionMethods()` 根据产品类别设置默认的检测项目。

付款
---------------------------------------------------------------------------
采购单付款方式分为三种：一次付清、分批付款和员工垫付。前两个适用于银行转帐；第三种适用采购报销。
### PurchasePayment Schema

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 和 Purchase 共用主键
`type`                              | bool      | No   | Lookup: 一次付清、分批付款、员工垫付
`divide_note`                       | string    | Yes  | 分批付款要求
`category`                          | int       | No   | 支出分类. extends Cost
`payment_way`                       | int       | No   | 付款方式. extends Cost
`receiver_account_id`               | int       | Yes  | 收款帐号. extends Cost
`account_id`                        | int       | Yes  | 付款帐号. extends Cost
`has_receipt`                       | int       | Yes  | 是否发票. extends Cost
`status`                            | int       | No   | 状态：已创建、已开始、已完成

### 评审

根据用户身份，评审分两种情况：

- hoardNewbie 提交的单子，必须经过 hoardPurchaser 评审；
- hoardPurchaser 和 pilePurchaser 提交的单子自动设置为“已评审”，简化操作；


拆分
---------------------------------------------------------------------------

换货也可以拆分, 换货本质上和采购单一样。

签收
---------------------------------------------------------------------------

`purchase/recieve` 由采购员完成。在此会检查商品附加属性设置 (`Purchase::checkSkuAdditionalProperty()`)。

检测
---------------------------------------------------------------------------
采购单的检测以采购明细为单位，签收以后进行。根据 `purchase_item.inspection_methods` 的值，分为两种情况：

1. 值为 null: 普通物资，采购员可以快速检验（`purchase-item/check`）完成检测；
2. 值不是 null: 需要质检员录入检测结果. 录入完成后，通过 `purchase-item/confirm-inspection` 完成检测；

不管那种情况, 都会将 `purchase_item.was_inspected` 设置为 1, 标记检测完成，同时触发 `PurchaseItem::EVENT_INSPECTION_CONFIRMED` 事件，进而动态判断采购单状态是否可以设置为“已检测”。


退换货
---------------------------------------------------------------------------
采购员也可以对不合格品选择“退货”和“换货”。退货将自动创建退货单；换货操作在退货单基础上由系统自动生成一个换货单。

换货单其实就是 `is_replacement` 列是 1 的采购单。采购员允许删除换货单。删除后，关联的采购清单会重新出现在待采购列表中。

留样
---------------------------------------------------------------------------

采购单中的核心物资必须留一定数量的样品。留够三年，之后可以丢掉。留样分为以下三种类型（使用 `specimen.type` 区分）：

1. 普通(common).这是最理想也是最简单的情况：检测的每个批次都附带的有样品，录入检测数量即可；
2. 共用(shared)：由于采购清单的限制，采购单经常出现这样一种情况：系统内显示的批次不同，但实际上是同一包料。这时只需要创建一个“普通”类型的留样记录，其余批次和它共用留样即可；
3. 强制(compulsory)：这是确保留样规则生效的最后一种情况，防止供应商不附带样品，此时将直接从大货中扣除样品；

### 权限

Role/Permission Name    | Parent        
------------------------|---------------
`createSpecimen`        | `qualityChecker`
`viewSpecimen`          | `createSpecimen`, `warehouseKeeper', `gwDirecto`, `productionDirector`, `saleDirecto` 

### 强制留样对采购金额无影响
入库的数量变少了，但是付给供应商的钱并没有少。换句话说，强制留样的商品公司也付费.

### 启用和撤销

- `unit/enable-specimen`
- `unit/revoke-specimen` (Edition 承载, 评审：质检员 → 采购员 → 生产经理)

操作
---------------------------------------------------------------------------

### 确认检测结果
建立在 PurchaseItem 上，`purchase-item/confirm-inspection`. 逻辑要点：

- 更新 `purchase_item.was_inspected` 值为 1;
- 生成批号, 并触发 `EVENT_INSPECTION_CONFIRMED` 事件, 此事件关联 handler 做两件事：
    1. 更新 purchase status 为“已检测”
    2. 若含有不合格品，给**采购单创建人**发送工单待处理通知, 完成不合格品的处理工作；
- 对合格品的记录，更新 `detect_purchase_item.opinion` 值为 PASS;

### 让步接收
质检检测发现不合格品后，采购员应对不合格品做出决定。如果选择”让步接收“，则需要进行评审，评审过程由[通用不合格品让步接收][generic-detection-concession]模型承载。本质上就是创建一条 `DetectionConcession` 记录.

### 调价
`purchase-item/edit-price` (Edition 承载). hoardNewbie 和 hoadPurchaser 允许操作。

评审顺序：申请人 → 采购主管；

### 变更备注
`purchase/tweak-note` (Edition 承载).

对于已经开始检测的采购单，采购员无法修改。此时可以申请对备注变更。

评审顺序：采购主管 → 质检主管；

### purchase-item/confirm-inspection

### purchase-item/check
采购员一键检测

### purchase-item/build-detection

另一种检测方式，省去重复录入上传报告，实现检测报告的共享使用。共享意味着 inspection 和 detection 可能存在一对多的可能，这就需要在 `detection/delete` 删除关联 inspection 前做一个判断：如果一个 inspection 还被其它 detection 使用，则仅删除关联表，否则连同 inspection 一并删除。详见 Issue 235.

Change Logs
---------------------------------------------------------------------------

- 2025-03-04 `Enh` 调整单价操作改用 Edition 承载；
- 2024-08-29 `Enh` 重新启用 deadline 列，作为“预计交货期”显示；
- 2024-07-01 `Enh` PurchaseItemFactor Schema, 增大 `base_d50` 和 `offset_d50` 精度各一位
- 2024-06-22 `Enh` Schema, 增加 `section_id` 列，精确到小部门；

日期        | 类别      | 动作 | 说明
------------|-----------|------|-------------------
2024-06-22  | Schema    | 改进 | 增加 `purchase.section_id`
2023-12-22  | Schema    | 新增 | PurchaseItemFactor 承载具体要求
2023-08-15  | Action    | 新增 | 让步接收申请评审
2023-08-11  | Scheme    | 新增 |`purchase_item.was_inspected`;

[generic-detection-concession]: /models/detection-concession.md
