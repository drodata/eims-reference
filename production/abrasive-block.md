# 磨料块

### AbrasiveBlock
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`mix_trace_id`                      | int       | No   | 混料编号
`presser`                           | int       | No   | 压块人 Lookup
`stock`                             | int       | No   | 库存

- `mix_trace_id` 和 `presser` 复合主键；
- 库存更新的地方：
    - `press/receive` 增加库存；
    - `rough-item/build` 装块环节。减少库存；

