# 监控

## 客户余额一致性 (balance-consistency)

此功能目的是为了找出客户余额表一致性的原因。系统一旦出现不一致的记录，即使报告给开发者。
命令：

```
# 查询最近生成的订货 Balance 记录是否存在一致性问题，是则打印出来
./yii monitor/balance-consistency

# 查询最近生成的订货 Balance 记录是否存在一致性问题，是则微信提醒给开发者
./yii monitor/balance-consistency notify
```
