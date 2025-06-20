# 问卷调查

基本上每年一次。大致过程：

- 通过 migration 新增 questionnaire.type
- 执行 `yii questionnaire/create` (修改 type 值)
- 调整 `backend\widgets\progress\QuestionnaireProgress` widget. 设置 title, deadline 等 
