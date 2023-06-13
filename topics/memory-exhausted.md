# 内存耗尽往往意味着代码有问题

财务反映 `deal/index` 页面打不开，显示著名的内存耗尽错误：

![内存耗尽报错](/images/memory-exhausted-error.png)

上图中堆栈中的错误信息具有误导性，实际原因跟图中代码无任何关系。之前遇到类似的问题，通过动态设置 `memory_limit` directive 可以解决此问题。在搜索中看到一条有用的信息，大致意思是说，如果代码出现内存耗尽的情况，多半是代码存在问题，因为通常情况下，代码运行时不会占用那么多的内存。

这次打算找到问题的根源。

在临时增加内存上限后，通过 Yii2 debug toolbar 看到 `deal/index` 页面执行一共消耗了 160MB. 点开发现了两条耗时最多的两条查询，

```sql
SELECT * FROM `om_deal_audit` WHERE `deal_id` IN (
    6259, 6258, 6257, 6256, 6255, 
    ...,
    6222);
SELECT * FROM `om_audit` WHERE `id` IN (
    '48159', '48160', '48161',
    ...,
    '48162');
```
上面的语句把系统中所有订单都计入在内。第一反应是难道是 DealSearch 页面 `joinWith()` 内包含 `audit` 关系导致的？转念一想：`order/index` 页面同样关联了 `audit` 模型，且订单的数量远大于 deal 数量，为啥 order/index 都没事儿,所以跟这里没有关系。

在 `order/index` 页面找到类似的语句，发现了明显的不同：

```sql
SELECT `om_order`.* FROM `om_order` LEFT JOIN `om_order_audit` ON `om_order`.`id` = `om_order_audit`.`order_id` LEFT JOIN `om_audit` ON `om_order_audit`.`audit_id` = `om_audit`.`id` LEFT JOIN `om_company` ON `om_order`.`customer_id` = `om_company`.`id` LEFT JOIN `om_address` ON `om_order`.`address_id` = `om_address`.`id` ORDER BY `c_time` DESC LIMIT 10
```

查询的末尾出现了 **LIMIT 10** 字样，而 deal 的相关语句下面没有。

还是通过 Debug Toolbar 'DB' section, 找到相关的查询语句，后面注明了此语句的调用位置，终于锁定到 `deal/_grid` 视图内.

```php
/* deal/_grid.php */
$items = $dataProvider->query->all();
...
```

看到这里瞬间豁然开朗：正是上面的语句把所有订单一次性读取出来，导致内存耗尽。解决方法很简单，把这个语句放在 if 内判断一下即可。

至此，这一类问题的原因终于找到。
