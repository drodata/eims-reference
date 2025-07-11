# 客户

概述
---------------------------------------------------------------------
### Company Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`category`                          | bool      | No   | Lookup 类别。客户(1)
`type`                              | bool      | Yes  | 客户类别：内贸(1)、外贸(2)
`email`                             | string    | Yes  | (外贸)客户邮箱地址
`last_deal_time`                    | int       | Yes  | 最后一次大货交付时间 
`recycles_at`                       | int       | Yes  | 下一次回收时间

操作
---------------------------------------------------------------------
### 删除

前置检查：是否存在未结清的单笔帐单或月结帐单，是则禁止删除。

### 批量移交
此操作通过 Modal 表单提交。分为显示表单 ('fetch-batch-transfer-form')
和提交表单('submit-batch-transfer-form')两部分。

里面有很多 jQuery 交互，单独存在在 `customer/_uex.php` 内，包含:

- 表格行任意位置单机选中复选框，并增加选中状态 (`table-active`)
- 全部选择后 toggle `.table-active` 类;
- GridView 内每次选择发生变化后，将选择的 keys 以 `data-keys` 形式存储在 `.batch-btn` 按钮中；

### 回收
等级为 C, D 的客户会通过 `customer/recycle` 定期自动回收。回收做以下事情：

1. 将当前负责人写入 `former_own_id`;
2. 将 `own_id` 设置为 0 (回收站);
3. 将当前时间写入 `recysles_at`, 记录回收时间；

### 导出欠款
`export/receivable`. saleDirector 和 accountant 可通过 `customer/index` 页面导出。

### Toggle
- `customer/toggle-is-new`: 销售经理可以调整新老客户；

客户地址
---------------------------------------------------------------------
### 操作
- **变更地址** 
  
  `address/edit`. 记录关键字段的修改过程。

变更日志
--------------------------------------------------------------------------
- 2025-05-13 `Add` 批量移交客户；
- 2024-07-17 `Enh` Schema, 新增`type` 和 `email` 列，记录外贸客户邮箱；
