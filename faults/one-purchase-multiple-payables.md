# 一个采购单出现多个 payable 记录

在供应商对账时发现此问题。导出的对账记录金额和不等于 payable 数量之和。
发现 CG-6092 居然有两条 payable 记录，且金额不一致。

如何避免此问题？

由于purchase 和 payable 之间是一对一关系。在 `purchase_payable` 表内把 
`purchase_id` 和 `payable_id` 都添加上 unique rule.
