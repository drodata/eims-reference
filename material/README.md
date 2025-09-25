# 物资
* [Material](/material/material.md)
* [Product](/material/product.md)
* [批次](/material/unit.md)
* [定向物资](/material/dedicated-unit.md)
* [提前备货](/material/pile.md)
* [消耗品领用](/material/requisition.md)
* [留样](/material/specimen.md)
* [登记回收](/material/fixture.md)
* [二次加工](/material/reprocessing.md)
* [Transfer 借调](/material/transfer.md)
* [Allotment 调拨](/material/allotment.md)
* [Defect 瑕疵](/material/defect.md)
* [Relocation 移位](/material/relocation.md)
* [分装 Package](material/package.md)
* [单位转换](/material/conversion.md)
* [固定资产](/material/device.md)
* [领料申请](/material/bucket.md)
* [Rename 改名](/material/rename.md)

角色权限
--------------------------------------------------------------------------

权限：

- `viewMaterialStock`: 查看物资库存
    - `viewMaterial`: 查看物资详情 (物资、容器详情页)
- `viewAllMaterial`: 查看所有物资 (material/index)
- `searchContainer`: 查看 container/index 页面；
- `handleSpu`: 用来处理 Spu, Sku, Brand 几个模型的权限汇总, 通过这个权限，可以轻松控制这三个模型的新建和显示；

固定资产类：

- `deviceKeeper`: 设备保管员，负责填写设备维保记录；
- `handleDevice` (deviceKeeper 子权限): 新建维保记录
- `manageDevice`: 查看设备及维保记录；
