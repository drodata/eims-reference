# 余额表

Balance
---------------------------------------------------------------------------
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`customer_id`                       | int       | No   | 
`action`                            | bool      | No   | 砂轮内部编码 Lookup
`currency`                          | bool      | No   | Lookup 币种
`direction`                         | bool      | No   | Lookup 方向： PRE, POST
`is_fake`                           | bool      | No   | 标记银行虚拟记录
`amount``                           | decimal   | Yes  | 
`balance`                           | decimal   | Yes  | 

### 动作

- 初始化: 1
- 汇款: 2
- 订货: 3
- 退货: 4
- 修改订单: 5
- 删除订单: 6
- 期末记账: 7
- 标记单笔结算客户对账单: 8

操作
---------------------------------------------------------------------------
### 纠正数量
管理员可通过 `balance/correct-amount` 直接纠正。

- 仅能纠正类别是付款且未在对账区间内的记录
