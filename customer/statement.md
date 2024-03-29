# 单笔帐单

Schema Statement
---------------------------------------------------------------------
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`customer_id`                       | int       | No   | LEGACY, NORMAL 
`type`                              | int       | No   | Lookup 状态：
`order_id`                          | int       | Yes  | 
`started_at`                        | int       | Yes  | 
`ended_at`                          | int       | Yes  | 
`deadline`                          | bool      | No   | 是否需要分批交付
`payment_status`                    | string    | No   | 
`status`                            | int       | No   | Lookup 状态：

### 遗留帐单和普通帐单
`type` 分为 legacy (1) 和 normal (2) 两种。前者没有对应的订单号，仅记录 `ended_at`.

其余全部为普通帐单，这种类型的帐单都有关联的订单。

### 付款状态

“未付款“(1) 和”已付款“(2). `status` 没有实际意义。

Feedbacks
---------------------------------------------------------------------

### 231205 未结清金额金额为负数

> 打扰下，麻烦看下岱勒系统里那个余额变动欠款数177,963.00，
> 但是账单还有340,562未关联，财务说能关联的账单都已关联其他的没办法关联

查看发现有几个帐单的未结清金额是负数。由于单笔帐单的未结清金额动态计算。
当订单退货时，若订单已有关联记录，将导致关联金额大于退货后的订单实际金额
, 进而出现其它订单无法关联的情况。

检查了新建退货单逻辑，发现**的确有一个前置检查,以防止上面情况发生**.
至于为什么上面的帐单仍会出现该情况，不得而知。
