# 台帐一致性检查

易耗品和劳保用品领用时需要领用人签字，同时在系统内记录。如何确保纸质台帐和电子台帐的一致性？实际操作中经常出现物资已经被领用，但是纸质或电子帐没有及时录入。Review 模型可以解决“账卡物”一致性问题。

大致思路：月初系统自动生成一个 review 记录，主要记录一个起止时间，通过评审功能强制上传纸质台帐扫描件，在评审中审核是否一致；同时在审核完成前，限制新增记录，从而从某种程度上达到自循环。

结构
---------------------------------------------------------------------------
### 权限
Role/Permission Name    |  Parents        | Description   
------------------------|-----------------|---------------
`review`                | `pilePurchaser` |                
`operateReview`         | `review`, `materialKeeper` |               
`viewReview`            | `review`, operateReview |               

### Review Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`branch_id`                         | int       | No   | 账套编号
`section_id`                        | int       | No   | 所属部门
`type`                              | int       | No   | 类别 Lookup: 低值易耗品、劳保物资
`start_date`                        | date      | No   | 开始日期
`end_date`                          | date      | No   | 结束日期

操作
---------------------------------------------------------------------------
### 新建记录
Console 端增加 `ReviewController` 完成记录的创建，目前有：

- `review/labour-protection` 用于生成劳保用品台帐；
- `review/consumable` 用于生成低值易耗品台帐；

### 评审
> warehouseKeeper → pilePurchaser → root

- 仓库核对手工台帐无误后，交给 pilePurchaser;
- pilePurchaser 扫描上传系统；

变更日志
--------------------------------------------------------------------------
- 2024-06-21 `Enh` Schema, 增加 `section_id` 列，精确到小部门；
