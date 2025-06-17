# 代加工

结构
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

操作
---------------------------------------------------------------------
### 新建
评审顺序：采购员 → 生产经理 → 总经理。评审通过后，微信通知到仓库管理员。

### 取料
取料通过关联表 `oem_item_pickup` 调用通用取料模块。

有的时候不需要实际取料，通过 `oem-item/toggle-pickness` 将 `oem_item.need_pick` 设置为 0, 达到无需取料的目的。

### 出库
搜集 `delivery_way` 和 `fetched_by` 两列信息。

### 发货

### 交付
发货完成后，可以在代加工单的基础上进行[交付][oem-delivery]。

Change Logs
---------------------------------------------------------------------
- 2023-12-12 新增 无需取料操作（借助 Edition 承载）；
- 2023-12-06 改进 状态: 新增“镀覆”类型 (值是 0), 专用于[订单镀覆加工][coating]
- 2023-11-21 新增 Logic: 不合格品退货；
- 2023-11-07 改进 Logic: 代加工交付不合格品增加让步接收操作；
- 2023-10-30 改进 Logic: 代加工取料使用单独的通用模型 Pickup 承载；
- 2023-10-30 改进 Schema: OemItem 增加 `name`, `specification`, `measurement_unit` 和 `need_pick` 

[coating]: /order/coating.md
[oem-delivery]: /purchasing/oem-delivery.md
