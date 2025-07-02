# 服务商付款

结构
--------------------------------------------------------------------------
### 权限
角色/权限      | Child         | Parent Roles
---------------|---------------|-----------------
`handleServer` | `viewServer`  | hoardNewbie, pilePurchaser, officeDirector, saler, staff, accountant
`manageServer` | `viewServer`  | root               
`viewServer`   |               |

- root 可以更改服务商的负责人，
- 给 handleServer 权限添加新角色时，注意检查评审人那里可以工作；
