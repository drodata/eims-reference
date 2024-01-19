# 磨料块

### AbrasiveBlock
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`mix_trace_id`                      | int       | No   | 混料编号
`presser`                           | int       | No   | 压块人 Lookup
`stock`                             | int       | No   | 库存

- `mix_trace_id` 和 `presser` 复合主键；
- 库存更新的地方：
    - 压块
        - 签收: `press/receive` 增加库存；
        - 撤销签收: `press/cancel-receive` 减少库存；
        - 作废: `press/discard` 减少库存；
    - 装块
        - 装块 `rough-item/build` 减少库存；
        - 作废: `rough/discard` 增加库存；
    - 异常 `Lapse::apply()装块`
