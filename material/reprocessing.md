# 二次加工

分为四大类：
1. 微粉部的 materialKeeper 可以提交常见类型的单子；
2. 制品2部的 materialKeeper 可以提交类型为“组团磨料研磨液“的加工单；
3. 制品1部的 gwMixer 可以提交类型为“去除杂质“的加工单；
4. 制品2部的 gaMixer 可以提交结合剂加工的单子（类似1部的原材料去除杂质.不同点是这里需要质检参与，1部的杂质去除不需要质检）；

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
`createReprocessing`    | `handleReprocessing`      | materialKeeper, gwMixer, gaMixer
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

入库后，可在物资详情页面重新下载标签。

### 入库

变更
--------------------------------------------------------------------------
- 2026-08-28 `enh` `gaMixer` 可以新建二次加工单（组团磨料的结合剂再处理）
- 2025-02-19 `Add` 交付单分装操作；
- 2025-01-20 改进 新增”组团磨料研磨液“类型，承载制品2部研磨液加工；
