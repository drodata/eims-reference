# 动态拉取下拉菜单改变数据的通用解决方法

新建采购单或订单时,最常见的一个操作就是通过选择供应商或客户，动态地加载对应的联系地址。以前都是在每个控制器内逐个声明一个类似 `actionFetchChangeData()` 的方法。其实这个操作完全可以抽象到 `site/fetch-change-data`, 再传递参数来实现。

场景一：订单中动态显示客户地址
--------------------------------------------------------------------------

### 视图内添加 JS 代码

```js
$('#deal-buyer_id').on('change', function() {
    var activeValue = $(this).val()
        , url = APP.baseUrl + 'site/fetch-change-data'
        , data = {key: 'buyer', id: activeValue}
    $.post(url, data, function(response) {
        if (response.alert === null) {
		    $('#deal-shipping_address_id').empty().html( response.entity );
        } else {
            window.alert(response.alert)
		    $('#deal-shipping_address_id').empty()
        }
    })
})
```

### 逻辑层

```php
// in SiteController
public function actionFetchChangeData()
{
	Yii::$app->response->format = \yii\web\Response::FORMAT_JSON;

    return Lookup::fetchChangeData(Yii::$app->request->post());
}
```

实际逻辑：

```php
// in Lookup::fetchChangeData()
$buyer = Buyer::findOne($id);

if ($buyer->owned_by == Yii::$app->user->id && $buyer->assistant) {
    $alert = static::hint('customer-taken-over');
} elseif (empty($buyer->addresses)) {
    $alert = '此客户尚未添加收货地址，无法提交订单。';
} else {
    $alert = null;
}

if (empty($buyer->addresses)) {
    $entity = null;
} else {
    $options = [Html::tag('option', '请选择收货地址', ['value' => ''])];
    foreach ($buyer->addresses as $address) {
        $options[] = Html::tag('option', $address->getDescription(), ['value' => $address->id]);
    }
    $entity = implode('', $options);
}

return [
    'alert' => $alert,
    'entity' => $entity,
];
```
