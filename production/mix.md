# 混料

取料
---------------------------------------------------------------------------
### MixItem
Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`id`                        | int       | No   | 
`mix_id`                    | int       | Yes  | 
`is_legacy`                 | int       | No   | 是否是历史遗留记录
`name`                      | string    | Yes  | 当 `is_legacy` 为 1 时必填
`bucket_selection_trace_id` | int       | Yes  | 追溯编码,追溯出库的物资.当 `is_legacy` 为 0 时必填
`quantity`                  | int       | No   | 取料数量

- 混料操作是中途新增功能，存在新老交替的情况：从仓库新领出的原辅料在交付时会强制录入追溯码，取这类料的时候需要录入追溯码；已经从仓库取出的料，由于无法批次，只能用文字简单描述(`name` 列);

创建混料单
---------------------------------------------------------------------------

### Mix
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`grinding_wheel_production_id`      | int       | No   | 生产编号
`grinding_wheel_code`               | int       | No   | 砂轮内部编码 Lookup
`mix_trace_id`                      | int       | Yes  | 追溯编码. `pack` scenario 时必填
`weight`                            | int       | Yes  | 混料总重量. `pack` scenario 时必填
`status`                            | int       | No   | 状态。已创建、已完成
`type`                              | int       | Yes  | 预留列。目前只有砂轮混料这一种类别；

### 操作

- 新建 `create`：确定生产编号和内部编号
- 包装 `pack`：确定追溯码和重量；
- 取消包装 `cancel-pack`：
