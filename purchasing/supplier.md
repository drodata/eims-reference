# 供应商

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
