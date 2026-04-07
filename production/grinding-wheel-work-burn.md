# 烧结

结构
--------------------------------------------------------------------------
### 

Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 与`grinding_wheel_work` 共用主键
`status`                            | int       | No   | Lookup `grinding-wheel-work-burn-status` 已创建(1)、已交付(5)、已接收(9)
`note`                              | string    | Yes  | 

操作
--------------------------------------------------------------------------
### 完成
`complete`:

- 触发 GrindingWheelWork `complete()` handler. 具体内容：
    - 更新 `process` 列为 null. 表示所有工序已完成;
    - 更新状态为”已完成“；
