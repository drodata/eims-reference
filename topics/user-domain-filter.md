# 帐号作用域 Filter

要解决的问题
---------------------------------------------------------------------------
不同的用户有自己的作用域，目前存在 `backend`, `gw` 和 `md` 三个域。要防止用户直接通过链接访问他不在的域内的内容. 以前的做法是在 Application `EVENT_BEFORE_ACTION` 内定义：

```php
// backend/config/main.php
return [
    ...
    'on beforeAction' => function ($event) {
        ...
        if (
            !Yii::$app->user->isGuest
            && !Yii::$app->params['isAssistant']
            && !Yii::$app->user->identity->checkDomain()
        ) {
            $event->isValid = false;
        }

    },
];
```

这种设置方法简单粗暴，一旦作用域不符合要求，直接显示空白页面，用户体验不是很好。还有一个更严重的问题：像设备管理码这样的链接，不需要也不能进行作用域检查。就会出现无法使用的问题。

使用专门的 ActionFilter 进行控制
---------------------------------------------------------------------------

详见 `common\filters\UserDomainFilter`. 优点是灵活且可定制交互内容。以仓管为例，直接在对应控制器内增加一行记录即可解决,例如：

```php
class InventoryItemController extends Controller
{

    public function behaviors()
    {
        return [
            'common\filters\UserDomainFilter',

            ...
    }
```
