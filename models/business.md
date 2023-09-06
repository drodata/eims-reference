# 往来单位

这个可以是客户、供应商、服务商等等的底层模型，只可惜想到得太晚。首次在领料单往来单位上应用。

`operateBusiness` 控制谁可以创建。有此权限的角色有： `saleDirector` 和 `productionDirector`. 此权限和 `createForeignBucket` 权限相互对应——能新建他用领料单的人，必定可以新建往来单位。

Schema
---------------------------------------------------------------------------
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   | 
`name`                              | string    | No   | 单位名称
`type`                              | int       | Yes  | 预留
`status`                            | int       | Yes  | 预留
