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
`flag`                              | bool      | Yes  | 通用标记列

### status
Code                    | Value  | Note
------------------------|--------|------------
CREATED                 |   11   | 已创建
PROCESSING              |   12   | 生产中
COMPLETED               |   13   | 已完成
DISCARDED               |   14   | 作废

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
`inspectin/inspect` 后会自动生成批号 `generateUnit()`, 在这里会自动查询最新尚未激活的容器号并写入系统。
### 分装
`warehousing/package-portal`. 标签打印人口页面。

为了处理已经入库的物资，单独做了一个 `warehousing/package-report` 页面来处理。
为了使返回按钮能正常工作，分装操作新增 'scene' 参数：

- 'default': 正常渠道，即入库前分装；
- 'patch': 处理已存在的记录，返回 'warehousing/package-report' 页面；

### 入库
### 变更内容

`warehousing/edit`. 通过 Edition 承载。

生产单在过程检验确认后，不允许修改。通过此操作可以修改以下列内容：

- `level`;
- `raw_material_supplier`;
- `strength`;
- `shape`;
### 作废
`warehousing/discard`. 一些生产单由于其它原因无法或不再需要入库时，通过此操作完成。

评审顺序：质检→ 仓库 → 生产经理

### 标记完成
`warehousing/mark-as-settled`. 提前预设 `flag` 值，通过操作改变该列值，
达到监测工作进度的目的。


变更
--------------------------------------------------------------------------
- 2025-02-21 `Schema` 增加 'flag' 列，处理类似分装操作等临时问题；
- 2024-12-03 调整 成品检验逻辑。标签改为自动打印，自动获取可用的容器编号
- 2024-12-02 新增 分装操作
