# AdminLTE

--------------------------------------------------------------------------
### 详情表格定制类名 .table-detail

不再使用类似 `.bg-info` 去逐个控制表头样式，直接全局自定义一个 `.table-detail` 类实现。

```php
<table class="table table-bordered table-detail" style="table-layout:fixed">
    <thead>
        <tr>
            <th width="25%"><?= $model->order->getAttributeLabel('id') ?></th>
            <td width="25%"><?= $model->order->id ?></td>
            <th width="22%"><?= $model->getAttributeLabel('created_at') ?></th>
            <td width="28%"><?= Yii::$app->formatter->asDate($model->created_at) ?></td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th><?= $model->order->customer->getAttributeLabel('short_name') ?></th>
            <td><?= $model->order->customer->getShortName() ?></td>
            <th><?= $model->order->getAttributeLabel('c_id') ?></th>
            <td><?= $model->order->creator->getName() ?></td>
        </tr>
    </tbody>
    <thead>
        <tr>
            <td colspan="4"><?= Html::fwicon('list') ?>明细</td>
        </tr>
    </thead>
    <tbody>
        <tr>
        </tr>
    </tbody>
```

### 自定义颜色
```
.bg-red,
.bg-yellow,
.bg-aqua,
.bg-blue,
.bg-light-blue,
.bg-green,
.bg-navy,
.bg-teal,
.bg-olive,
.bg-lime,
.bg-orange,
.bg-fuchsia,
.bg-purple,
.bg-maroon,
.bg-black,
.bg-red-active,
.bg-yellow-active,
.bg-aqua-active,
.bg-blue-active,
.bg-light-blue-active,
.bg-green-active,
.bg-navy-active,
.bg-teal-active,
.bg-olive-active,
```
