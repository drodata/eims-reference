# 代加工

概述
---------------------------------------------------------------------
### Oem Schema

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`type`                              | bool      | Yes  | Lookup 加工类别
`seller_id`                         | int       | No   | 供应商
`address_id`                        | int       | Yes  | 收货地址
`delivery_way`                      | bool      | Yes  | Lookup 交付方式
`fetched_by`                        | int       | Yes  | 交付人
`status`                            | int       | No   |

### OemItem Schema

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`oem_id`                            | int       | No   | 
`action`                            | bool      | Yes  | 预留列, 作用不大
`sku_id`                            | int       | Yes  | 商品编号
`name`                              | bool      | No   | 品名
`specification`                     | string    | Yes  | 规格
`quantity`                          | int       | No   | 数量
`measurement_unit`                  | bool      | Yes  |
`need_pick`                         | bool      | No   | 是否需要取料

代加工单
---------------------------------------------------------------------

### 新建
评审顺序：采购员 → 生产经理 → 总经理。评审通过后，微信通知到仓库管理员。

### 取料

取料通过关联表 `oem_item_pickup` 调用通用取料模块。
### 出库
搜集 `delivery_way` 和 `fetched_by` 两列信息。
### 发货

交付
---------------------------------------------------------------------

 * @property integer $id
 * @property integer $oem_delivery_id
 * @property string $action
 * @property string $inspection_id
 * @property string $container_id
 * @property integer $sku_id
 * @property string $inspection_methods
 * @property string $price
 * @property integer $quantity

### OemDeliveryItem Schema
OemDelivery 和 OemDeliveryRefuse 共用此表格。

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   |
`oem_delivery_id`                   | int       | No   | 交付编号
`action`                            | int       | No   | Lookup 动作：交付、退回
`inspection_id`                     | int       | Yes  | 退货的报告编号
`container_id`                      | int       | Yes  |
`sku_id`                            | int       | Yes  | 
`inspection_methods`                | int       | Yes  |
`price`                             | int       | Yes  | 实际上是必填项
`quantity`                          | int       | No   | 数量，退货时为负数


### DetectOemDeliveryItem Schema

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`oem_delivery_item_id`              | int       | No   | 
`detection_id`                      | int       | No   | 
`opinion`                           | int       | Yes  | Lookup 意见: 通过(1)、让步(2)、退回(4)

检测
---------------------------------------------------------------------

- `oem-delivery/inspection-portal`: 检测入口
- `oem-delivery/confirm-inspection`: 检测结果确认. 确认会做以下事情：
    1. 生成批次；
    2. 如果含有合格品，将对应 `detect_oem_delivery_item.opinion` 的值设置为“通过”；
    3. 如果含有不合格品，微信通知交付单创建人；

处理不合格品
---------------------------------------------------------------------

`oem-delivery/trouble-portal` 显示不合格品列表，采购员再次申请让步接受. 这里的逻辑比采购单让步接受更加合理：`detect_oem_delivery_item.opinion` 的值在让步接受评审通过后自动写入。这样的设置让入库前置检测变得很简单——只需要看对应的 opinion 是否都设置了。

退货
---------------------------------------------------------------------
让步接收不能解决所有问题，和采购单类似，比较严重的质量问题需要有一个通道重新退回给加工上重新加工。

和采购单不同的是，不设置换货选项。换货本质就是先退货，再新建交付单,这点和销售订单类似。

### OemDeliveryRefuse Schema

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 和交付单共用主键，约定只能退货一次
`address_id`                        | int       | No   | 生产编号
`status`                            | int       | No   | Lookup 状态：已创建、已出库、已完成
`delivery_way`                      | int       | Yes  | Lookup 交付方式
`fetched_by`                        | int       | Yes  | 交付人

入库
---------------------------------------------------------------------

入库前置检查：

1. 让步接收的必须完成评审；
2. 退货的必须将退货发货。用这个限制防止退货单的修改；


Change Logs
---------------------------------------------------------------------
- 2023-12-06 改进 状态: 新增“镀覆”类型 (值是 0), 专用于[订单镀覆加工][coating]
- 2023-11-21 新增 Logic: 不合格品退货；
- 2023-11-07 改进 Logic: 代加工交付不合格品增加让步接收操作；
- 2023-10-30 改进 Logic: 代加工取料使用单独的通用模型 Pickup 承载；
- 2023-10-30 改进 Schema: OemItem 增加 `name`, `specification`, `measurement_unit` 和 `need_pick` 

[coating]: /order/coating.md
