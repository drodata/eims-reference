# Inventory 库存日志

操作
---------------------------------------------------------------------------

### 替换
`inventory-item/displace` root 可以操作，在 inventory 详情页面有此按钮。

场景：仓库在给订单 20176 取料时，不小心输入错了容器编号，本来是 C2434, 却输入成了 C2435. 发货后发现此问题，有修改的需求。

操作步骤：

1. 手动修改 `selection.unit_id` 
2. 手动修改 `order_delivery_item.unit_id` 
3. 找到取料记录对应的 inventory, 执行 displace 操作；
