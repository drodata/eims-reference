# Section 核算部门

Branch 可以把应用分成不同的账套，同一个账套内如果再想细分就没有办法。基于此，新建此模型。

Section 最主要的作用是有自己的 materialKeeper 和仓库。目前标记核算部门的模型有：

- Purchase
- Manufacture
- Bucket

结构
---------------------------------------------------------------------
### 权限
Role/Permission Name    | Parents
------------------------|---------------
`gaWarehouseKeeper`     | 

- gaWarehouseKeeper 的主要作用是在首页显示订单中需要取料的组团磨料 tab.

### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   | 账套
`name`                              | string    | No   | 
`keeped_by`                         | int       | No   | 物资保管员
`alias`                             | string    | No   | 预留

