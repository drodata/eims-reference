# 镀覆订单加工

Intro
---------------------------------------------------------------------

### 旧版本的问题

- **订单和代加工单分离,无法根据订单查看加工状态** 
  
  最初的设计很简单：仅仅使用 `coating_shipment` 关联表记录了镀覆前发出的包裹信息。后来镀覆慢慢开始提交代加工单，
  这就导致镀覆单和代加工单分离,不便于销售掌握加工进度；

原来的 send 和 receive 两个操作可以作废；状态进行扩展。

### 新版本

- 通过在 coating 表中增加外键列 `oem_id`, 以建立两者的关联。
- oem 新增特殊的 COATING type (值是 0), 用来标记订单镀覆的代加工。此类型不会和其他类型一起出现在代加工单列表内。

- 订单终审自动生成 oem 不可行，无法确定 seller. 只能采购手动生成 (`oem/build`)
- 新状态：已创建、已生成、已取料、已发货、已交付、已入库；
- 订单作废后对镀覆订单的影响；
- 原来的 `coat/send` 更改状态，生成 `coating_shipment`

状态常量                | 状态值 | 值改变场景
------------------------|--------|------------
`STATUS_CREATED`        |   1    | order/create 随着订单自动创建
`STATUS_CONFIRMED`      |   11   | oem/build 采购手动新建代加工单
`STATUS_FETCHED`        |   12   | oem/fetch 仓库出库后
`STATUS_SENT`           |   13   | oem/deliver 发货后
`STATUS_DELIVERED`      |   21   | oem-delivery/create 采购新建交付单
`STATUS_RECEIVED`       |   22   | oem-delivery/receive 仓库签收
`STATUS_INSPECTED`      |   23   | oem-delivery/confirm-inspection 质检检测
`STATUS_COMPLETED`      |   29   | oem-delivery/store 仓库入库

### 权限

- `handleCoating`: hoardPurchaser, hoardNewbie, saler, warehouseKeeper, qualityChecker, fetcher;
    - `viewCoating`: productionDirector, president, root

Structure
---------------------------------------------------------------------
### Coating Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`order_service_id`                  | int       | No   | 用 `order_service.id` 作为主键
`demand`                            | string    | No   | 加工要求 
`shipping_address_id`               | int       | No   | 地址
`status`                            | int       | No   | Lookup 状态：详见上面
`oem_id`                            | int       | Yes  | 代加工单

### CoatingShipment Schema

旧版本用来记录发货状态的关联表，已弃用。

Actions
---------------------------------------------------------------------
### 新建
业务员新建订单时，如果勾选了镀覆加工选项，新建订单后将自动创建关联的 coating 记录。

### 加工
采购员通过 `coating/build` 创建关联的 oem 记录。该操作的主要工作有：
- 新建 oem 记录,主要就是确定供应商和收货地址
- 更新 `coating.oem_id`, 建立关联；

之后走代加工评审流程。

### 取料
仓库对评审通过的单据进行取料。实际调用关联的代加工单进行取料操作.

### 出库
实际使用代加工出库操作 (`oem/fetch`)，出库后**会更新 coating 状态为“已出库”**.

### 发货
Fetcher 发货. 这里实际使用的是 `oem/deliver` 操作，区别在于**操作后会更新 coating 状态为“已发出”**.

### 交付
采购员新建交付单. 这里实际使用的是 `oem-deliver/deliver` 操作，操作后会更新 coating 状态为“已交付”;

### 签收
仓库签收。操作后会更新 coating 状态为“已签收”; 签收后，交付单禁止修改；

### 检测
质检检测。操作后会更新 coating 状态为“已检测”;

### 处理不合格品
交付的商品若不合格。需采购对不合格品进行处理（内部使用 `oem-delivery/trouble-portal`）

- 让步接收
- 退回

### 入库
仓库入库。操作后会分别更新 coating 和 oem 状态为“已完成”;

Change Logs
--------------------------------------------------------------------------
- 2023-12-06 `Enh` 新增 `coating.oem_id`, 使用代加工承载镀覆类加工过程；
