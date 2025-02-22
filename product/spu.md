# Spu
- Sku 规格的排列顺序依据 `specification.property_id` 升序排列。
- Sku 名称命名规格 `<sku-specification-group> + <spu-name>`, 例如 "128G 白色 iPhone 15".

### 评审

`common/params-local.php` 内有一个 `enableSpuAudit` 开关。
控制新建产品时是否需要管理员评审。默认为开启状态。

操作列表
--------------------------------------------------------------------------

### 调整规格
`spu/adjust-specification`

- 为防止自动备货紊乱，劳保产品禁止采购员调整（管理员可以）；
