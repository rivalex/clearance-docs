# 发布资源

Clearance 通过 `vendor:publish` 标签发布三类资源：配置、迁移和翻译文件。配置和迁移由安装程序自动处理（参见[安装](/zh/guide/installation)）；本页介绍如何手动发布它们，以及如何发布翻译文件。

## 配置

```bash
php artisan vendor:publish --tag=clearance-config
```

将 `config/clearance.php` 发布到你的应用中。每一个键及其默认值和用途，请参见[配置参考](/zh/guide/configuration)。

## 翻译

```bash
php artisan vendor:publish --tag=clearance-translations
```

这会将内置的 9 种语言（`ar`、`en`、`es`、`fr`、`hi`、`it`、`pt`、`ru`、`zh`）复制到你应用中的 `lang/vendor/clearance/{locale}/ui.php`。要添加新语言或覆盖某个现有语言中的字符串，请在该目录下新增一个语言目录，并以 `en/ui.php` 作为规范的键参考。所有语言都暴露完全相同的键集合，并在每次变更时都通过一个专门的测试进行验证。

## 覆盖面板布局

`layout` 配置键控制全页组件所使用的 Blade 布局：

```php
// config/clearance.php
'layout' => null, // host app's default (config('livewire.layout'))
```

保持为 `null` 以使用你应用的默认 Livewire 布局，或将其设置为一个特定的布局视图，以便在自定义外壳中渲染面板。

## 迁移

Clearance 的迁移由 `php artisan clearance:install` 自动发布并运行——如果 `roles` 表尚不存在，安装程序还会先发布并运行 Spatie 的迁移。完整的分步流程请参见[安装](/zh/guide/installation)，以及其中关于切勿在全新环境中直接运行 `migrate` 的[危险提示](/zh/guide/installation#运行安装程序)。

## 相关链接

- [配置参考](/zh/guide/configuration)——每一个配置键
- [安装](/zh/guide/installation)——完整的安装程序流程
- [迁移](/zh/guide/migration)——为已有的 Spatie 安装使用 `clearance:backfill`
