# 通用领料申请

涉及到的权限有：

- `viewBucket`
    - `manageBucket`: materialKeeper
    - `receiveBucket`: “自用”领料交付后签收
    - `deliverBucket`: “他用”领料交付后发货
    - `createBucket`
        - `createPersonalBucket`
        - `createForeignBucket`
        - `createExperimentBucket`
        - `createHybridBucket`: 能创建上面三种领料中的至少两种。

不用类型的领料单中对应列值一览表：

类型    | `type`    | `is_foreign`  | `is_standard`
--------|-----------|---------------|----------------
自用    | 0         | 0             | 0
他用    | 0         | 1             | 0
生产    | 1         | 0             | 1
试验    | 2         | 0             | 0

申请
---------------------------------------------------------------------------

### Bucket
Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`id`                        | int       | No   | 
`branch_id`                 | int       | No   | 
`section_id`                | int       | No   | 使用部门
`type`                      | int       | No   | 标记具体领用类型，包括：通用、砂轮生产、砂轮实验
`is_foreign`                | int       | No   | 标记是外用还是自用，前者有发货的功能；
`is_standard`               | int       | No   | 1 表示有具体的生产单；0表示砂轮实验领用原辅料； 
`business_id`               | int       | Yes  | 往来单位。适用于 foreign bucket
`delivery_way`              | bool      | Yes  | 交付方式。适用于 foreign bucket
`address_id`                | int       | Yes  | 往来单位的发货地址。适用于 foreign bucket
`is_lock`                   | int       | No   | 预留？
`description`               | string    | Yes  | 申请说明。当 `is_standard` 为 0 时必填
`status`                    | int       | No   | 已创建、准备中、已完成、已结束

### BucketItem Schema

Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`id`                        | int       | No   | 
`bucket_id`                 | int       | Yes  | 
`name`                      | int       | No   | 基体、磨料、结合剂等
`specification`             | string    | No   | 规格
`quantity`                  | int       | No   |
`measurement_unit`          | int       | No   | 单位
`note`                      | string    | Yes  |
`need_pick`                 | bool      | No   | 是否需要取料

- `bucket_item.name` 可以判断出原辅料是否需要过程追溯，目前基体不需要过程追溯；

### 评审顺序
超硬材料内，productionDirector 可同时新建自用和他用的普通领料单；saleDirector 仅能新建他用的普通领料单。申请人直接找 decisionMaker 评审即可。

制品中心内, ”生产“和”实验“两种类型的领料单，顺序是：申请人 → 决策人；”普通“领料单的顺序：申请人 → 仓库 → 生产经理。

### 事件

`Bucket::EVENT_DELIVERY_CHANGED` 以下操作将触发该事件，完成领料单状态的更新。

- 领料交付单交付时 (`bucket-delivery/create`). 若所有条目均已交付则状态更新为“已完成”，否则更新为“准备中”；
- 领料明细变更数量时 (`bucket-item/tweak-quantity`);


取料
---------------------------------------------------------------------------
### BucketSelection
Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`id`                        | int       | No   | 
`bucket_item_id`            | int       | No   | 
`bucket_delivery_id`        | int       | Yes  |
`bucket_selection_trace_id` | int       | Yes  | 在`SCENARIO_TRACE_DELIVERY`下必填
`unit_id`                   | int       | Yes  | `bucket_item.need_pick` 为 1 时必填 
`warehouse_id`              | int       | Yes  | `bucket_item.need_pick` 为 1 时必填
`quantity`                  | int       | No   |

- 砂轮生产追溯引入 `bucket_selection_trace_id`: 仓库创建交付单时，如果领料名称是磨料，则强制输入追溯编号;

### 无需取料
类型是“默认”的领料申请单存在无法取料的情况：要发的料是检测样品，没有物资批号。为了解决这类情况。新增 `bucket_item.need_pick` 布尔列标记这类情况。

默认情况下， `need_pick` 值是 1, 遇到特殊情况时，可通过“无需取料”操作 (`bucket-item/toggle-pickness`) 将值更新为 0. “无需取料”操作通过 Editon 承载。终审通过后，自动生成一条伪取料记录。“伪”的意思是 BucketSelection 的 `unit_id` 和 `warehouse_id` 值为 null.

交付
---------------------------------------------------------------------------
支持分批交付、终止交付。

### BucketDelivery

Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`id`                        | int       | No   | 
`bucket_id`                 | int       | Yes  | 
`is_foreign`                | int       | No   | 标记是外用还是自用，前者有发货的功能；
`received_by`               | int       | Yes  |
`received_at`               | int       | Yes  |
`delivery_way`              | int       | Yes  | foreign 时必填
`fetched_by`                | int       | Yes  | foreign 时必填
`delivered_at`              | int       | Yes  | foreign 时必填
`status`                    | int       | No   | 已创建、已领取、已完成

追溯
---------------------------------------------------------------------------
由 `BucketItem::needTrace()` 实现是否追溯. 领料单中出现以下商品时启用追溯（输入追溯码）：

1. 类型是"砂轮生产"的领料单中的磨料和结合剂；
2. 类型是"普通"的领料单中的结合剂；

所谓追溯，就是在新建领料交付单的基础上，对交付明细 (BucketSelection) 新建一个额外的表—— BucketSelectionTrace,
已记录追溯编号和交付时的库存数。

### BucketSelectionTrace Schema

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | Trace ID
`bucket_selection_id`               | int       | No   | 取料编号
`stock`                             | int       | No   | 库存

### 领料退回
领料开始追溯的物资，会因为工艺调整等原因重新退回仓库。借助通用的 Withdrawal 模型，使用关联表 `withdraw_bucket_selection_trace` 将其和 BucketSelectionTrace 关联起来。

gwMixer 根据追溯编码，在追溯详情页面发起退回申请。仓库确认入库后，自动生成一条数量变动记录 (BucketSelectionTraceSnap)

数量变动日志
---------------------------------------------------------------------------
对砂轮生产来说，发生的每一次数量变动都会记录，方便查询。

### BucketSelectionTraceSnap Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`bucket_selection_trace_id`         | int       | No   | 追溯码
`action`                            | int       | No   | 动作 Lookup: '领料'(1), '混料'(2), '丢失'(3)、'退回'(4)
`quantity`                          | int       | No   | 数量
`mix_item_id`                       | int       | Yes  | 当动作是 '混料' 时必填
`lapse_id`                          | int       | Yes  | 当动作是 '丢失' 时必填
`withdrawal_id`                     | int       | Yes  | 当动作是 '退回' 时必填

此表的记录全部用用户的其它操作自动触发生成，包括：

- `bucket-delivery/create`: 新建动作为 '领料' 的记录；
- `mix-item/create`: 新建动作为 '混料' 的记录；
- `withdrawal/deliver`: 退回单交付时新建动作为 '退回' 的记录；

每当新增或删除记录时将会触发更新 `bucket_selection_trace.stock` 的 handler (`syncTraceStock()`).

其它常用操作
---------------------------------------------------------------------------

### 调整数量
`bucket-item/tweak-quantity` 借助通用 Edition 模型实现。原辅料评审后允许改数。数量变更后会触发原辅料单状态更新，确保已完成交付的状态能更新。

### 作废
在评审完成后、尚未交付前的这段时间，申请人可以作废单据。

> 评审顺序: 申请人 → 所在部门物资保管员

作废按钮同时满足以下条件才会显示：
- 具有 `createBucket` 权限；
- 状态是“已创建”

### 终止交付

`bucket/terminate`. 借助 Editon 实现，本质是将领料单状态设置为“已结束”。

假如领料单是分批交付, 申请人可以提交终止交付申请。此操作的前置条件必须满足：

- 领料单状态是“交付中”（存在至少一个已完成的交付单）；
- 不能含有已取料但未交付的取料记录；
- 不能存在尚未完成的交付记录；

Change Logs

- 2024-07-17 Add 增加“作废"操作；
- 2024-06-13 Enh BucketSelectionTraceSnap. 增加 `withdrawal_id` 列，新增退货动作；
- 2024-05-28 Enh 增加 `section_id` 列，区分使用部门；
- 2023-11-15 Enh `logic`: 调整`Bucket::EVENT_DELIVERY_CHANGED` 触发时机
  
  对仓库来说，领料单取完料以后，这个领料单就结束了，不需要再显示在首页。
  剩下的就是交付单的处理（签收或发货）, 这一点和订单和订单交付类似。
  调整触发时机后,就能解决该问题；

---------------------------------------------------------------------------
日期        | 类别              | 动作  | 说明
------------|-------------------|-------|-------------------
2023-11-15  | Model             | 改进  | BucketDelivery 新增 'fetched' status, 自用领料创建后为“已领取”
2023-10-08  | Schema            | 改进  | `bucket_selection` 表内 `unti_id` 和 `warehouse_id` 两列类型改成可以为空,以便存储伪取料记录
2023-10-08  | Schema            | 新增  | `bucket_item.need_pick` 区分取法取料的情况
2023-09-04  | Schema            | 新增  | `bucket.branch_id`, 正式在超硬账套内使用，接下来弃用 Picking 模型（自产微粉领料申请）
2023-07-21  | Model             | 新增  | `BucketSelectionTraceSnap` 数量变动日志，在追溯页面更好地呈现数量变动过程
2023-07-10  | BucketDelivery    | 新增  | `is_foreign`, `delivery_way`, `fetched_by`, `delivered_at` 四列，支持外用类的领料
2023-07-10  | Bucket            | 新增  | `is_foreign`, `business_id`, `delivery_way` 和 `address_id` 四列，支持外用类的领料
