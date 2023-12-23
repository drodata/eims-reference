# 在事务外调用 trigger() 导致脏数据

没有检测项目的采购单需要采购员通过 `purchase-item/check` 操作完成快速检测。

```php
public function check()
{
    $this->was_inspected = 1;
    $this->on(self::EVENT_AFTER_UPDATE, [$this, 'buildPassedInspection']);

    if (!$this->save()) {
        throw new \yii\db\Exception($this->stringifyErrors());
    }

    $this->trigger(self::EVENT_INSPECTION_CONFIRMED);

    return [true, '检测已完成', '/dashboard/progressing-purchase-item'];
}
```

事件`EVENT_INSPECTION_CONFIRMED`绑定的 handler 负责更新采购单的状态:

```php
// in PurchaseItem
public function syncPurchaseStatus($event)
{
    ...

    $purchase->status = Purchase::STATUS_INSPECTED;

    if (!$purchase->updateAttributes(['status'])) {
        throw new Exception('Failed to update.');
    }
}
```
上面两段代码的问题在于：**handler 在 `save()` 内置的事务以外被执行，因此无法保证数据一致性。**

由于前端展示部分出现问题，确认合格后的记录仍然可见，导致用户对同一条记录连续进行多次操作。第一次之后的操作就出现了一个现象：页面提示保存错误，但是系统依然生成了多余的检测记录。

原因就在于错误是 handler 抛出的，但是由于在事务之外，因此没能阻止事务内的代码(`buildPassedInspection()`)回滚, 造成了脏数据。

### 解决
只需要将

```php
$this->trigger(self::EVENT_INSPECTION_CONFIRMED);
```

移至 `buildPassedInspection()` handler 内即可解决该问题。


教训
--------------------------------------------------------------------------
能用 handler 搞定的，不要自定义事件；非要自定义事件，切记在事务内触发。
