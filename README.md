# 首页

================ THIS IS THE TEMPLATE ==============

---------------------------------------------------------------------------
Column                      | Type      | Null | Note
----------------------------|-----------|------|-------
`branch_id`                 | int       | No   | 
`name`                      | varchar   | No   | 模型 key. `demand-item` 等
`type`                      | int       | No   | `DELAY_DEADLINE` 等
`reason`                    | varchar   | No   | 
`status`                    | int       | No   | 1: 已创建 4: 已拒绝 9: 已完成

状态常量                | 状态值 | 值改变场景
------------------------|--------|------------
`STATUS_CREATED`        |   1    | 
`STATUS_REFUSED`        |   4    | 终审拒绝时 
`STATUS_COMPLETED`      |   9    | 终审通过时
