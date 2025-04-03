# 交付

结构
---------------------------------------------------------------------------

### OrderDelivery Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`order_id`                          | int       | No   | 
`address_id`                        | int       | Yes  | 
`delivery_way`                      | int       | Yes  | 
`fetched_by`                        | bool      | Yes  | 是否需要分批交付
`status`                            | int       | No   | Lookup `order-delivery-status` 

状态：已创建(1)、已出库(3)、已交付(5)、已送达(7)

### OrderDeliveryItem Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`inspection_methods`                | set       | Yes  | 实际按照必填执行
`inspection_flag`                   | bool      | Yes  | null 表示无需检测或历史记录；0: 未检测；1: 已检测
`hash`                              | string    | Yes  |

逻辑要点

- 检测项目为必填。最少应包含“外观尺寸”检测，以确保质检能看到所有交付明细；
- `order/init-delivery` 时动态填充 (借助 `Product::getDefaultInspectionMethods`) `inspection_methods` 和 `inspection_flag` 两列：
- 只能检测一次,即只能有一个 detection. 后期如有多次检验的需求，再放开；

操作
---------------------------------------------------------------------------

### 检测
订单的检测之前建立在 Goods 上。为了减少退货的发生，强制要求订单出库前检测，即建立在 OrderDeliveryItem 上。

### 打印
交付详情页面显示，打印的交付清单不显示该列。

### 变更收货人
`order-delivery/change-address` 新建交付单时，`order_delivery.address_id` 自动继承 
`order.address_id`. 业务员可通过此操作申请更改收货人信息。

评审顺序：业务员 → 销售经理 → 仓管员

变更
--------------------------------------------------------------------------
- 2025-04-03 改进 新增 `order_delivery.address_id`. 收货地址变更通过评审完成；
