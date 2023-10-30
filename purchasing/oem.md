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

代工交付单
---------------------------------------------------------------------

Change Logs
---------------------------------------------------------------------
- 2023-10-30 改进 Logic: 代加工取料使用单独的通用模型 Pickup 承载；
- 2023-10-30 改进 Schema: OemItem 增加 `name`, `specification`, `measurement_unit` 和 `need_pick` 
