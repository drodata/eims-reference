# 通用原辅料领用

Bucket
---------------------------------------------------------------------------
### Schema
Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`id`                        | int       | No   | 
`type`                      | int       | No   | 标记具体领用类型，包括：砂轮生产、砂轮实验
`is_standard`               | int       | No   | 1 表示有具体的生产单；0表示砂轮实验领用原辅料； 
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
`received_by`               | int       | Yes  |
`received_at`               | int       | Yes  |
`status`                    | int       | No   | 

其它常用操作
---------------------------------------------------------------------------

### 调整数量
`bucket-item/tweak-quantity` 借助通用 Edition 模型实现。原辅料评审后允许改数。数量变更后会触发原辅料单状态更新，确保已完成交付的状态能更新。

Change Logs
---------------------------------------------------------------------------

- 2023-06-14 弃用 `bucket/confirm-delivery`, BucketDelivery 增加签收操作后，此操作已无存在意义；
- 2023-06-14 新增 `bucket-item/tweak-quantity` 评审后可以微调数量;
