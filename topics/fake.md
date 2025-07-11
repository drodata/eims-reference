# Fake

> :bell: 全局常量 `FAKE`
> 
> 在 `common/configs/constant` 内定义。控制系统内所有与发票相关的显示。

常规操作步骤：

- fake/deal: 自动生成订单
- fake/earnings 生成回款记录
- fake/purchase 2024-09 参照当月超硬系统内采购单生成采购记录(逐月)
- fake/cost 2024-06 付款记录(延期三个月, 实际生成日期是 2024-09 付款记录)

Actions
---------------------------------------------------------------------------

Command | Example | Note
--------|----------|-------
`earnings` | `fake/earnings` | 自动生成收款记录至当前时间
`buyer` | `fake/buyer` |
`seller` | `fake/seller` |
`deal` | `fake/deal 2018-05`, `fake/deal 2018-05-01` | 以指定时间段的 Order 为蓝本，复制到五年后的 Deal 中
`scale-deal` | `fake/scale-deal 2023-01 2`, `fake/scale-deal 235 0.5` | 调整指定月份或编号的订单
`purchase` | `fake/purchase 2023-01` | 以**超硬系统中**指定月份的采购单为蓝本，自动生成采购单
`fake/scale-purchase` | `fake/scale-purchase 2023-01 8` | 调整指定月份采购单数量，次参数是调整基数，默认值是 2
`cost` | `fake/cost 2023-01` | 为指定月份的采购单生成付款记录
`fake/scale-cost` | `fake/scale-cost 2023-01 2`, `fake/scale-cost 235 10` | 调整指定月份或编号的付款记录
`fake/scale-purchase` | `fake/scale-purchase 2023-01 8` | 调整指定月份采购单数量，次参数是调整基数，默认值是 2
`fake/delete-purchase` | `fake/scale-purchase 2023-01`, `fake/scale-purchase 567` | 删除指定月份或具体某个采购单

订单
---------------------------------------------------------------------------

生产虚拟订单。时间跨度默认是 5 年，例如想生产 2023年8月份的 deals. 则应输入命令 `fake/deal 2018-08`. 这个日期参数不仅支持按月份批量生产，还支持更加细粒度的按照日期逐天生产。

例如，我想让最新的订单日期是 2023年5月15日, 那么 5月份之前的可以使用传入类似 `2018-05` 整月生产；5月1日至15日这半月，可以传入 `2018-05-01` 一天一天生产。

三种参数格式：

1. `null` (`fake/deal`) 自动生成订单至现在。这是最好用的一种形式；
2. 按月生产 (`fake/deal 2018-05`) 。生成 2023-05 月所有订单；
3. 按日生产 (`fake/deal 2018-05-09`) 。生成 2023-05-09 当天的订单；

### 调整金额

> `fake/scale-deal $slug $base`

此命令用来批量调整订单的数量（即金额）。参数如下：

- `$slug` 订单的筛选条件。有两种格式：
    - 年月格式，例如 '2011-03'.可以筛出某个月的所有订单；
    - 单个订单编号。调整指定订单；
- `$base` 调整基数。订单明细的数量去乘以此基数，例如 2, 0.5, 100 等；

范例：

- `scale-deal 2024-02 2`: 将 2024年2月所有订单金额翻倍
- `scale-deal 2356 2`: 将订单 2356 金额翻倍

> 注意：此命令更新金额的同时，会同时更新关联收款记录的金额（如果有的话）

### 闰年的因素

此功能是 2023 年增加的，往前倒五年，2020 年是闰年，2月份有 29 天，所以是 365 * 5 + 1 = 1826, 这里多加的一天就是 2020 年2月29日。明年 (2024 年)执行此代码时，就需要根据闰年的情况对该数字进行调整。

### 删除指定订单

批量生成的订单有时需要删除指定的订单，登陆销售帐号可以完成此操作。

### 调整单价数量比例
`deal-item/shift`. 由 root 在 deal-item/index 操作。

适用场景：财务会计算全年平均销售单价, 和采购平均单价进行比对。自动生成的数据有时会产生“销售均价低于采购均价”的现象。
为解决此问题，需要针对单个订单明细，在确保总金额不便的前提下，增加数量并减少单价，或者反之。

### 对关联回款记录的影响
因为回款的金额等于订单金额，因此订单金额变动后，需要更新更新关联的回款记录。

这也意味着**回款记录不需要单独调整，直接调整订单即可。**

回款
---------------------------------------------------------------------------

无需设置任何参数,直接执行 `fake/earnings` 即可自动生成从最后一笔收入记录至当前时间内的收款记录。

### 导出客户欠款表

财务临时需求。由于虚拟账单不管生成订单还是回款，并没有在关联的记账表记录，因此这里采用一种笨办法，动态获取：将 Deal 和 Earnings 记录按照时间顺序合并。

采购单
---------------------------------------------------------------------------

参数：

- `$yearMonth` (Required): e.g.'2023-05': 以超硬系统制定月份的采购单为蓝本，生成采购单

### 导出采购明细

这里创建的采购单没有任何检测及入库记录。因此导出采购明细导出的是原生的 `purchase-item` 表，这点和超硬和制品中心不同，后两者导出的是 `detect_purchase_item` 表。

在 DownloadForm 内创建 `SCENARIO_NATIVE_PURCHASE_ITEM` 加以区分。

### 调整金额
> `fake/scale-purchase $slug $base`

- 指定月份： `scale-purchase 2024-02 2` (将 2024年2月所有采购单金额翻倍)
- 指定单号： `scale-purchase 2356 2` (将采购单 2356 金额翻倍)

此命令用来批量调整采购单的金额（内部通过调整数量实现）. 参数用法同订单金额调整.

付款
---------------------------------------------------------------------------

参数：

- `$yearMonth` (Required): e.g.'2023-05': 这里的月份指的是采购单的时间，也就是说，将指定时间内的采购单按供应商汇总出来，在之后的某个时间集中付款, 目前的延迟期限是 三个月，即5月的采购单集中在8月付清。

### 调整金额
`./fake scale-cost` 两种形式：

- 指定月份： `scale-cost 2024-02 2` (将 2024年2月所有付款金额翻倍)
- 指定单号： `scale-cost 2356 2` (将付款记录 2356 金额翻倍)

### 修改金额
>  `./fake update-cost-amount <id> <value>`
可将指定付款单的金额设置为任意值。`<id>` 是单号，`<value>` 是新值.

### 修改供应商名称
>  `./fake update-cost-seller <id> <seller-id>`

有时需要在保证每年总应付金额不变的情况下，微调供应商的应付金额。
比较省事的办法就是通过更改一个付款单的卖家名称，
从而实现**“应付金额对冲”的效果**。


付款金额
---------------------------------------------------------------------------
`fake/scale-cost $slug $base`

此命令用来批量调整供应商的付款金额.参数用法同订单金额调整.



### 导出应付账款

和虚拟的应收款对应：将 Purchase 和 Cost 记录按照时间顺序合并。

杂项
---------------------------------------------------------------------------

- 导出客户清单: `export/buyer`

Change Logs
---------------------------------------------------------------------------

- 2025-06-19 新增 `fake/update-cost-seller` 更改付款记录的卖家名称；
- 2025-06-06 调整 `fake/cost` 延迟期限增加到三个月；
- 2025-01-22 增加全局常量 `FAKE`;
- 2023-06-13 `fake/deal` 支持参数为 null, 可以直接生产从最近一个订单到现在的订单；
