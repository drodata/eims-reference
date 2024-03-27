# Warehousing 微粉生产

结构
---------------------------------------------------------------------

### Warehousing Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`product_id`                        | int       | No   | Product 外键。品名和标准粒度
`level`                             | bool      | No   | Lookup 品级 (WarehousingLevel)
`sku_id`                            | int       | No   | 
`container_id`                      | int       | No   | 入库后的容器号
`unit_id`                           | int       | No   | 
`raw_material_supplier`             | int       | No   | 原料厂家 Lookup `diamond-maker`
`shape`                             | int       | No   | 形貌 Lookup `powder-shape`
`strength`                          | int       | No   | 强度。Lookup `diamond-strength`
`number`      `                     | string    | Yes  | 生产批号。8位数字。例如 24030101
`quantity`                          | int       | No   | 入库数量
`stock`                             | int       | No   | 当前库存
`status`                            | int       | No   | Lookup 状态：

### status
Code                    | Value  | Note
------------------------|--------|------------
PREPARING               |   1    | 
DELIVERING              |   2    | 
COMPLETED               |   3    | 
TERMINATED              |   14   | 终止交付

### WarehousingProcessingInspection

状态
Code                    | Value  | Note
------------------------|--------|------------
CREATED                 |   1    | 已创建
INSPECTED               |   3    | 已检测
CONFIRMED               |   5    | 已审核

操作
---------------------------------------------------------------------

### 新建
### 过程检
### 成品检
### 入库
### 变更内容

`warehousing/edit`. 通过 Edition 承载。

生产单在过程检验确认后，不允许修改。通过此操作可以修改以下列内容：

- `level`;
- `raw_material_supplier`;
- `strength`;
- `shape`;
