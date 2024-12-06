# 采购单付款/报销
这是以采购单为单位的精准付款模型，相对应的，Cost 可以更加灵活地申请供应商付款。

付款方式支持银行转账、电子承兑和报销三种方式。

结构
--------------------------------------------------------------------------
### PurchasePayment Schema

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`type`                              | bool      | No   | 类别：一次付清、分批付款、普通报销
`divide_note`                       | string    | Yes  | 分批付款要求 
`category`                          | int       | No   | 支出分类
`payment_way`                       | bool      | No   | 付款方式
`receiver_account_id`               | int       | Yes  | 收款账户
`has_receipt`                       | bool      | No   | 是否有发票
`status`                            | int       | No   | Lookup 状态：

Type 分类
Code                          | Value  | Note
------------------------------|--------|------------
`TYPE_ONE_TIME_PAY`           |   1    | 一次付清
`TYPE_DIVIDE`                 |   2    | 分批交付
`TYPE_FETCH`                  |   3    | 报销

操作
--------------------------------------------------------------------------
### 付款
财务在此页面需要选择付款银行，保存后自动生成相应的 cost 记录，就好像通过 cost/create 提交一样

### 直接付款
`direct-pay`. 电子承兑类型的付款，不再强制填写付款银行。借鉴 Cost 直接付款的方法，仅标记状态。