# 通用的AJAX Modal
要点：

- 控制器: `site/fetch-modal-data`
- 视图: `site/_modal`;
- `Lookup::fetchModalData()`: 真正的配置逻辑;


1️⃣ 配置按钮
--------------------------------------------------------------------------
在model 的 actinLink() 方法内配置。关键是 key, section, id 三个参数。'id' 和 'key' 锁定哪个模型；'section' 锁定哪个部分；
```php
case 'view-editions':
    $route = 'javascript:void(0)';
    $options = [
        'title' => '查看变更',
        'class' => 'generic-modal',
        'icon' => 'history',
        'data' => [
            'modal' => [
                'key' => 'oem-delivery-item',
                'section' => 'edition-list',
                'id' => $this->id,
            ],
        ],
    ];
    if (empty($this->editions)) {
        $hint = '未变更';
        break;
    }
    break;
```
2️⃣ 配置参数
--------------------------------------------------------------------------
在 `Lookup::fetchModalData()` 内配置：**关键是新增对应的 view file (modal content)**

```php
case 'oem-delivery-item':
    $model = OemDeliveryItem::findOne($id);
    $modalId = implode('-', [$key, $section, 'modal']);

    if ($section == 'edition-list') {
        $header = '变更记录';
        $viewFile = '/oem-delivery-item/_detail-editions';
        $viewParams = [
            'model' => $model,
        ];
    }
    break;
```

3️⃣ 编写 View 
--------------------------------------------------------------------------

4️⃣ 放置按钮
--------------------------------------------------------------------------
