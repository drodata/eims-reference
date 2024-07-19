# 供应商
结构
--------------------------------------------------------------------------
### 类别
- A: 核心物资. hoardPurchaser 负责；
- B: A 和 C 重叠部分
- C: 消耗品。pilePurchaser 负责；

付款
--------------------------------------------------------------------------
有两种途径向供应商付款：

1. 以采购单为单位提交采购单付款单；
2. 以供应商为单位提交供应商付款申请单；

具有 handlePurchasing 权限的用户可以申请上面两种付款申请。

### 采购单付款申请

评审顺序：

- hoardNewbie → hoardPurchaser → cfo
- pilePurchaser → cfo (有采购单保障，无需经过 hoardPurchaser)
- hoardPurchaser → cfo;

### 供应商付款申请
评审顺序：

- hoardNewbie → hoardPurchaser → cfo
- pilePurchaser → hoardPurchaser → cfo;
- hoardPurchaser → cfo;

变更日志
--------------------------------------------------------------------------
- 2024-07-19 `Enh` schema, 新增 `seller.level`
