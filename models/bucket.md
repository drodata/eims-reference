# 通用领料申请

涉及到的权限有：

- `viewBucket`
    - `manageBucket`: 仓库
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

### Bucket Schema
Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`id`                        | int       | No   | 
`branch_id`                 | int       | No   | 
`type`                      | int       | No   | 标记具体领用类型，包括：通用、砂轮生产、砂轮实验
`is_foreign`                | int       | No   | 标记是外用还是自用，前者有发货的功能；
`is_standard`               | int       | No   | 1 表示有具体的生产单；0表示砂轮实验领用原辅料； 
`business_id`               | int       | Yes  | 往来单位。适用于 foreign bucket
`delivery_way`              | bool      | Yes  | 交付方式。适用于 foreign bucket
`address_id`                | int       | Yes  | 往来单位的发货地址。适用于 foreign bucket
`is_lock`                   | int       | No   | 预留？
`description`               | string    | Yes  | 申请说明。当 `is_standard` 为 0 时必填
`status`                    | int       | No   | 已创建、准备中、已完成

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

`Bucket::EVENT_DELIVERY_CHANGED` 以下将触发该事件，完成领料单状态的更新。

- 自用型领料交付单签收时 (`bucket/receive`);
- 外用型领料交付单交付时 (`bucket/deliver`);
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
`status`                    | int       | No   | 

追溯
---------------------------------------------------------------------------

### BucketSelectionTrace Schema

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | Trace ID
`bucket_selection_id`               | int       | No   | 取料编号
`stock`                             | int       | No   | 库存

数量变动日志
---------------------------------------------------------------------------
对砂轮生产来说，发生的每一次数量变动都会记录，方便查询。

### BucketSelectionTraceSnap Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`bucket_selection_trace_id`         | int       | No   | 追溯码
`action`                            | int       | No   | 动作 Lookup: '领料', '混料', '调整'
`quantity`                          | int       | No   | 数量
`mix_item_id`                       | int       | Yes  | 当动作是 '混料' 时必填

此表的记录全部用用户的其它操作自动触发生成，包括：

- `bucket-delivery/create`: 新建动作为 '领料' 的记录；
- `mix-item/create`: 新建动作为 '混料' 的记录；

每当新增或删除记录时将会触发更新 `bucket_selection_trace.stock` 的 handler (`syncTraceStock()`).

其它常用操作
---------------------------------------------------------------------------

### 调整数量
`bucket-item/tweak-quantity` 借助通用 Edition 模型实现。原辅料评审后允许改数。数量变更后会触发原辅料单状态更新，确保已完成交付的状态能更新。

Change Logs
---------------------------------------------------------------------------
日期        | 类别              | 动作  | 说明
------------|-------------------|-------|-------------------
2023-10-08  | Schema            | 改进  | `bucket_selection` 表内 `unti_id` 和 `warehouse_id` 两列类型改成可以为空,以便存储伪取料记录
2023-10-08  | Schema            | 新增  | `bucket_item.need_pick` 区分取法取料的情况
2023-09-04  | Schema            | 新增  | `bucket.branch_id`, 正式在超硬账套内使用，接下来弃用 Picking 模型（自产微粉领料申请）
2023-07-21  | Model             | 新增  | `BucketSelectionTraceSnap` 数量变动日志，在追溯页面更好地呈现数量变动过程
2023-07-10  | BucketDelivery    | 新增  | `is_foreign`, `delivery_way`, `fetched_by`, `delivered_at` 四列，支持外用类的领料
2023-07-10  | Bucket            | 新增  | `is_foreign`, `business_id`, `delivery_way` 和 `address_id` 四列，支持外用类的领料
