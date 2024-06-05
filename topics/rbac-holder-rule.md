# 通用的 HolderRule 实现通用权限控制

> 后续新增 Role 都可以先添加 `operateModel` 为子权限，后期需要时只需要填充 HolderRule 逻辑

起因
---------------------------------------------------------------------------
如何防止用户通过链接（例如 `view?id=xxx`）直接操作其他用户提交的内容？常见的做法：

1. 创建类似 `viewPost` 和 `viewOwnPost` 这样的权限,再增加一个 Rule 进行控制；
2. 自定义一个 filter 进行判断；

方法一的问题在于不具有通用性：如果系统内的模型过多, 就得创建足够多的权限，管理起来也很麻烦；方法二也行，创建过类似 `CustomerOwnerFilter`.

有没有更好的方法？

通用的 HolderRule
---------------------------------------------------------------------------

新建一个通用的权限 `operateModel`, 在其上添加一个通用的规则 `HolderRule`. 下面用一个例子简单说明：

假设我们想使用这个方法防止业务员通过链接查看其他不是他负责的客户的信息。

1. 增加曾加权限，让 `handleSale` 成为 `operateModel` 的父权限.这样我们就能就在 can() 中传递参数；

2. 在 OrderController ACF 内配置如下：

    ```php
    [
        'allow' => true,
        'actions' => ['update', 'pay', 'request-post-pay'],
        'roles' => ['operateModel'],
        'roleParams' => [
            'key' => 'order',
            'host' => Order::findOne(['id' => Yii::$app->request->get('id')]),
        ],
    ],
    ```
3. 在 backend\rbac\HolderRule 内添加逻辑代码：
    
    ```php
    public function execute($userId, $item, $params)
    {
        $key = ArrayHelper::getValue($params, 'key');
        $host = ArrayHelper::getValue($params, 'host');

        switch ($key) {
            case 'order':
                /* @var $host Order */
                return  is_null($host->customer->assisted_by)
                    ? $host->customer->own_id == $userId
                    : $host->customer->assisted_by == $userId;
                break;
        }
    }
    ```

ACF 本身就是一个强大的 filter, 添加一个 Rule 就能搞定，何必再去新建类似 CustomerOwnerFilter 呢？
以后有其它需求，如法炮制。
