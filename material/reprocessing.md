# 二次加工

分为三大类：
1. 制品1部的 gwMixer 可以提交类型为“去除杂质“的加工单；
2. 制品2部的 materialKeeper 可以提交类型为“组团磨料研磨液“的加工单；
3. 微粉部的 materialKeeper 可以提交其它常见类型的单子；

结构
--------------------------------------------------------------------------
### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   | 
`section_id`                        | int       | No   | 
`type`                              | bool      | No   |
`status`                            | int       | No   | Lookup 状态：

### 权限
角色/权限               | Children                  | Assignment
------------------------|---------------------------|-----------------------
`createReprocessing`    | `handleReprocessing`      | materialKeeper, gwMixer
`handleReprocessing`    | `viewReprocessing`        | qualityChecker
`manageReprocessing`    | `viewReprocessing`        | productionDirector, gwDirector
`viewReprocessing`      |                           |

交付单操作
--------------------------------------------------------------------------
### 新建
还有一个 `reprocessing/fixed-create`, 用来单独新建类型是”去除杂质“的单子。不支持修改（删除）

### 快速检测
`reprocessing/send-insepect`. 专用于“去除杂质“类的单子，相当于送检和检测二合一。
### 分装
`reprocessing/package-portal`. 目前**自产的(即品牌是“亚龙”)微粉、整形料和破碎料**需要分装(via `needPack()`)。

通过 `checkPackage()` 判断分装操作是否完毕，作为入库的前置条件。

### 入库

变更
--------------------------------------------------------------------------
- 2025-02-19 `Add` 交付单分装操作；
- 2025-01-20 改进 新增”组团磨料研磨液“类型，承载制品2部研磨液加工；
