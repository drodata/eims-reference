# 采购单

操作列表
---------------------------------------------------------------------------

### purchase-item/build-detection

另一种检测方式，省去重复录入上传报告，实现检测报告的共享使用。共享意味着 inspection 和 detection 可能存在一对多的可能，这就需要在 `detection/delete` 删除关联 inspection 前做一个判断：如果一个 inspection 还被其它 detection 使用，则仅删除关联表，否则连同 inspection 一并删除。详见 Issue 235.