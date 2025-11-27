# 承兑

结构
--------------------------------------------------------------------------

### Schema
Column                              | Type      | Null | Note
------------------------------------|-----------|------|-------
`id`                                | int       | No   | 
`number`                            | string    | No   | 票号
`amount`                            | decimal   | No   | 金额
`expire_date`                       | date      | No   | 到期日期
`type`                              | bool      | No   | 1(纸质), 2(电子) `AcceptanceType`
`status`                            | int       | No   | 1 可用、2 不可用 `AcceptanceStatus` 
`source`                            | int       | No   | 来票单位
`received_by`                       | int       | Yes  | 送票人
`receive_note`                      | string    | Yes  | 送票备注
`destination`                       | int       | Yes  | 取票单位
`sent_by`                           | int       | Yes  | 取票人
`sent_at`                           | int       | Yes  | 取票时间
`send_note`                         | string    | Yes  | 取票备注
`created_at`                        | int       | No   | 
`created_by`                        | int       | No   | 

状态：已创建(1)、已生效(2)、已作废(4)
