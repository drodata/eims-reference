# 交付

检测
---------------------------------------------------------------------------
订单的检测之前建立在 Goods 上。为了减少退货的发生，强制要求订单出库前检测，即建立在 OrderDeliveryItem 上。

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

