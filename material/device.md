# 固定资产
结构
--------------------------------------------------------------------------
### ArchivalDevice Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 和 Device 公用主键
`type`                              | bool      | No   | Lookup `archival-device-type`. 注销(1)、报废(4)
`status`                            | bool      | No   | Lookup `archival-device-status`. 已创建(1)、已完成(9)
`note`                              | string    | No   | 说明 

操作
--------------------------------------------------------------------------
### 注销
`archival-device/create` 由 deviceKeeper 创建。

> 评审顺序：申请人 → 生产主管 → 总经理
