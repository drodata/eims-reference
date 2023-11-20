# 备货

分为[客户备货][customer-hoard]和[公司备货][company-hoard]两种。

Structure
---------------------------------------------------------------------------

入口地址根据 name 不同而不同，例如微粉备货入口是 `micro-diamond-hoard/create`.

`Hoard::build()` 负责写入逻辑，和 `PlanItem` 记录保持同步。

### 权限

- `hoardPurchaser`: 
- `hoardNewbie`: 
- `handleHoard`: 处理 A 类物资采购. `hoardPurchaser` 和 `hoardNewbie` 拥有此权限；
- `manageHoard`: 管理或查看(`hoard-plan-item/index`)所有备货. `productionDirector`, `saleDirector` 和 `warehouseKeeper`
- `viewHoard`
    - `createHoard`
        - `createCustomerHoard`: 新建客户备货 (`saler`)
        - `createCompanyHoard`: 新建公司备货 (`hoardPurchaser`, 'hoardNewbie`, `productionDirector`, `gwDirector`, `warehouseKeeper`)

### Hoard Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`scope`                             | int       | No   | Lookup 客户(1)和公司(2)
`name`                              | int       | No   | Lookup 品名：微粉、原生料和破碎料
`specification`                     | string    | Yes  | 规格. 部分品名(破碎料)可用
`quantity`                          | int       | No   | 备货数量
`deadline`                          | int       | No   | 期望交期
`customer_id`                       | int       | Yes  | 客户备货时必填

### DiamondHoard Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 和 Hoard 共用主键
`maker`                             | int       | No   | Lookup `diamond-maker`
`strength`                          | int       | No   | Lookup `diamond-strength`
`size`                              | int       | No   | Lookup `diamond-size`

[customer-hoard]: customer/hoard.md
[company-hoard]: purchasing/hoard.md
