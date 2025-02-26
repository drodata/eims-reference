# 生产标准

结构
--------------------------------------------------------------------------
### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`name`                              | string    | No   | 
`status`                            | int       | No   | Lookup 状态：已创建(0)、已生效(1)、已作废(4)
`type`                              | bool      | Yes  | 类型：砂轮、模块、特1
`press_instruction`                 | string    | Yes  | 压块指令
`burn_instruction`                  | string    | Yes  | 烧结指令
`base_specification`                | string    | Yes  | 基体规格

三种类型：

1. 标准砂轮 `WHEEL`: 此类型必须填写压块要求、烧结要求和基体规格
2. 磨块 `BLOCK`:此类型不需要基体；
3. 特1砂轮 `HOOP`:没有压块和烧结环节，需要基体

### Proportion Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`formula_id`                        | int       | No   | 
`name`                              | int       | No   | 原生料、微粉、破碎料、CBN, 结合剂
`code`                              | string    | Yes  | 磨料标准粒度中的 ID 或 Lookup code
`specification`                     | string    | Yes  | 规格
`quantity`                          | int       | No   | 
`measurement_unit`                  | bool      | No   | 
