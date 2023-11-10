# 批次

类型
--------------------------------------------------------------------------

Code             | Name         | Model                     | 批号生成位置
-----------------|--------------|---------------------------|-------------------------------------------------
`CONTAINER`      | 自产         |`Warehousing`              |`inspection/inspect`
`PURCHASE`       | 采购         |`Purchase`                 |`purchase-item/confirm-inspection`
`OEM`            | 代加工       |`Oem`                      |`oem-delivery/confirm-inspection`
`REPROCESSING`   | 二次加工     |`Reprocessing`             |`reprocessing-delivery/confirm-inspection`
`FAILED_REJECT`  | 不合格退货   |`Reject`                   |`reject/confirm-inspection`
`GRINGING_WHEEL` | 资产砂轮     |`GrindingWheelDelivery`    |`grinding-wheel-delivery/confirm-inspection`
`FIXTURE`        | 登记回收     |`Fixture`                  |`fixture/confirm-inspection`
`MANUFACTURE`    | 自产(新型)   |`Manufacture`              |`manufacture/confirm-inspection`
`DENY`           | 退货(Deal)   |`Deny`                     |`deny/confirm-inspection`
`PROCESSING`     | 复合片加工   |`ProcessingDelivery`       |`processing-delivery/confirm-inspection`
`MACHINING`      | 复合片加工   |`MachiningDelivery`        |`machining-delivery/confirm-inspection`

说明：`PROCESSING` 预留，复合片加工改用更简单的 `MACHINING` 模型承载。

### 自产
