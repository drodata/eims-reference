# 通用原辅料领用

Bucket
---------------------------------------------------------------------------
### Schema
Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`id`                        | int       | No   | 
`type`                      | int       | No   | 标记具体领用类型，包括：通用、砂轮生产、砂轮实验
`is_foreign`                | int       | No   | 标记是外用还是自用，前者有发货的功能；
`is_standard`               | int       | No   | 1 表示有具体的生产单；0表示砂轮实验领用原辅料； 
`business_id`               | int       | Yes  | 往来单位。适用于 foreign bucket
`delivery_way`              | bool      | Yes  | 交付方式。适用于 foreign bucket
`address_id`                | int       | Yes  | 往来单位的发货地址。适用于 foreign bucket
`is_lock`                   | int       | No   | 预留？
`description`               | string    | Yes  | 申请说明。当 `is_standard` 为 0 时必填
`status`                    | int       | No   | 已创建、准备中、已完成

### 事件
`Bucket::EVENT_DELIVERY_CHANGED` 原辅料交付签收后和原辅料数量变更后将触发该事件，完成原辅料状态的更新。

### BucketItem

Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`id`                        | int       | No   | 
`bucket_id`                 | int       | Yes  | 
`name`                      | int       | No   | 基体、磨料、结合剂等
`specification`             | string    | No   | 规格
`quantity`                  | int       | No   |
`measurement_unit`          | int       | No   | 单位
`note`                      | string    | Yes  |

- `bucket_item.name` 可以判断出原辅料是否需要过程追溯，目前基体不需要过程追溯；

取料
---------------------------------------------------------------------------
### BucketSelection
Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`id`                        | int       | No   | 
`bucket_item_id`            | int       | No   | 
`bucket_delivery_id`        | int       | Yes  |
`bucket_selection_trace_id` | int       | Yes  | 在`SCENARIO_TRACE_DELIVERY`下必填
`unit_id`                   | int       | No   | 
`warehouse_id`              | int       | No   | 
`quantity`                  | int       | No   |

- 砂轮生产追溯引入 `bucket_selection_trace_id`: 仓库创建交付单时，如果领料名称是磨料，则强制输入追溯编号;

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

其它常用操作
---------------------------------------------------------------------------

### 调整数量
`bucket-item/tweak-quantity` 借助通用 Edition 模型实现。原辅料评审后允许改数。数量变更后会触发原辅料单状态更新，确保已完成交付的状态能更新。

Change Logs
---------------------------------------------------------------------------
日期        | 类别              | 动作  | 说明
------------|-------------------|-------|-------------------
2023-07-10  | BucketDelivery    | 新增  | `is_foreign`, `delivery_way`, `fetched_by`, `delivered_at` 四列，支持外用类的领料
2023-07-10  | Bucket            | 新增  | `is_foreign`, `business_id`, `delivery_way` 和 `address_id` 四列，支持外用类的领料
