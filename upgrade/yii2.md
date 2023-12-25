# Yii2

2.0.49
--------------------------------------------------------------------------
```
$ composer update yiisoft/yii2
Loading composer repositories with package information
Updating dependencies
Lock file operations: 0 installs, 1 update, 0 removals
  - Upgrading yiisoft/yii2 (2.0.45 => 2.0.49.3)
Writing lock file

Installing dependencies from lock file (including require-dev)
Package operations: 0 installs, 1 update, 0 removals
  - Downloading yiisoft/yii2 (2.0.49.3)
  - Upgrading yiisoft/yii2 (2.0.45 => 2.0.49.3): Extracting archive

Generating autoload files
26 packages you are using are looking for funding.
Use the `composer fund` command to find out more!
```
出现一个提示:

> The "fxp/composer-asset-plugin" plugin (installed globally) was skipped because it requires a Plugin API version ("^1.0") that does not match your Composer installation ("2.2.0"). You may need to run composer update with the "--no-plugins" option.

这是因为当前 Composer 版本是 2.2.22
