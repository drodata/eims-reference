# 压块

新建
---------------------------------------------------------------------------

### Press
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`mix_trace_id`                      | int       | No   | 混料编号
`presser`                           | int       | No   | 压块人 Lookup
`quantity`                          | int       | No   | 
`status`                            | int       | No   | 状态。已创建(1)、已完成(9)

签收
---------------------------------------------------------------------------
签收操作做以下事情：

1. 改变单据状态；
2. 更新磨料块的库存；

撤销签收
---------------------------------------------------------------------------

如果操作员签收后发现混料编号选择错误，管理员可以撤销签收操作。然后操作员可以重新修改、签收。

1. 改变单据状态；
2. 更新磨料块的库存；
