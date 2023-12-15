# Deal 订单

Deal Schema
---------------------------------------------------------------------
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`buyer_id`                          | int       | No   | 
`shipping_address_id`               | int       | No   | 
`deadline`                          | int       | No   | 
`need_division`                     | bool      | No   | 是否需要分批交付
`devision_note`                     | string    | Yes  | 
`status`                            | int       | No   | Lookup 状态：

### status
Code                    | Value  | Note
------------------------|--------|------------
PREPARING               |   1    | 
DELIVERING              |   2    | 
COMPLETED               |   3    | 
TERMINATED              |   14   | 终止交付

DealItem Schema
---------------------------------------------------------------------
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`action`                            | bool      | No   | Lookup 动作
`deal_id`                           | int       | Yes  | 
`deny_id`                           | int       | Yes  | 退货单号
`deal_delivery_item_id`             | int       | Yes  | 
`is_cargo`                          | bool      | No   | 
`name`                              | bool      | Yes  | Lookup 品名
`specification`                     | string    | Yes  | 规格
`price`                             | decimal   | Yes  | 
`quantity`                          | int       | No   |
`standard_id`                       | int       | No   | Reserved 

### action
Code       | Value  | Note
-----------|--------|------------
DEAL       |   1    | 
DENY       |   2    | 

### name
Code                    | Value  | Note
------------------------|--------|------------
`CBN_GRINDING_WHEEL`    |   1    | 
`DIAMOND_GRINDING_WHEEL`|   2    | 
`PDC_MACHINING`         |   9    | 
`CBN_POWDER`            |   21   | 
`DIAMOND_POWDER`        |   51   | 
`OEM_GRINDING_WHEEL`    |   62   | 代加工砂轮

含有“代工砂轮”的订单关联的生产单 `is_oem` 值为 1.
