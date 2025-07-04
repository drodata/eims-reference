# 采购调配
调配单相当于把采购的商品重新卖给供应商。

结构
---------------------------------------------------------------------------
### ExchangeItem Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`exchange_id`                       | int       | Yes  | 
`sku_id`                            | int       | Yes  | 已弃用
`name`                              | bool      | yes  | Lookup 品名
`specification`                     | string    | yes  | 规格
`price`                             | string    | No   | 
`quantity`                          | int       | No   |
`measurement_unit`                  | bool      | No   |

操作列表
---------------------------------------------------------------------------
### 评审
> purchaseNewbie → purchaseDirector

### 新建
采购员可以创建调配单
### 记账
`exchange/pay` 记账。将单子标记为“已付款”,同时写入 payable 记录。
由于供应商账户有预付款和应付款的区分，所以用 `pay-portal` 页面引导。内部使用 `exchange/pay` 操作.
### 取料 
**调配单不支持分批交付，所有物资取料完成方可出库**。
`exchange/pick-portal`. 借助 ExchangeItemPickup 连接通用 Pickup 模型实现。

2023.12.19 之前的取料记录存储在 `ACTION_EXCHANGE` inventory 内。

### 出库
`exchange/fetch`. 出库前会检查是否已付款。
### 交付
`exchange/deliver`. 交付后给制单员推送发货通知。

Change Logs
--------------------------------------------------------------------------
- 2024-05-13 `Adj` 增加评审环节；
- 2023-12-19 `Enh` Schema：借鉴 OemItem, 新增 `name`, `specification`, `measurement_unit` 列，简化提交过程；使用通用 Pickup 模型承载取料过程；
- 2023-11-16 `Enh` 逻辑：记账后仍可以修改；

[purchase-audit]: /purchasing/purchase.md#评审
