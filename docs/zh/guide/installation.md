# 安装

## 环境要求

完整的[环境要求表](/zh/guide/introduction#环境要求)请参见简介页面——PHP ^8.3、Laravel ^11.0|^12.0|^13.0、spatie/laravel-permission ^6.0、livewire/livewire ^3.0|^4.0、livewire/flux ^2.14。

## 安装此包

```bash
composer require rivalex/clearance
```

## 运行安装程序

```bash
php artisan clearance:install
```

::: danger 请始终运行 clearance:install，切勿直接运行 migrate
Clearance 自身的迁移依赖于 Spatie 的 `roles`/`permissions` 数据表预先存在；安装程序会先发布并运行 Spatie 的迁移，然后再运行自己的迁移。如果你在一个全新环境中直接运行 `php artisan migrate`，而 Spatie 尚未配置好，你会收到一条可操作的错误提示，告诉你应改为运行安装程序。
:::

### 可选参数

| 参数 | 说明 |
|---|---|
| `--user=ID` | 安装完成后为某个用户分配 `super_admin` 角色 |
| `--role=NAME` | 将 `clearance-access` 分配给某个角色（若不存在则创建） |
| `--super-admin-role=NAME` | 将现有角色提升为 `super_admin`（跳过交互式提示） |
| `--force` | 即使已经安装过，也强制重新运行 |

### 安装程序执行的操作

1. 发布 `config/clearance.php`。
2. 发布 Clearance 的迁移文件。
3. 检测 Spatie Permission 的数据表——如果 `roles` 表不存在，则先自动发布并运行 Spatie 的迁移。
4. 运行所有待执行的迁移。
5. 创建 `clearance-access` 以及全部 5 个精细化能力权限：`clearance-permissions-write`、`clearance-roles-write`、`clearance-guards-write`、`clearance-settings-write`、`clearance-users-write`。
6. 创建 `super_admin` 角色，并分配所有 `clearance-*` 权限（增量式操作——重复运行不会移除已有权限）。
7. 写入 `storage/.clearance-installed` 作为幂等性标记。

::: tip 正在从已有的 Spatie 安装迁移？
请使用 `php artisan clearance:backfill`，而不是从零开始，并先阅读[迁移指南](/zh/guide/migration)——其中涵盖了 `super_admin` 冲突、自定义模型子类以及看守器导入等内容。
:::

## 在你的 User 模型中添加 HasClearance

要使用上下文授权方法（`$user->canIn()`、`$user->hasRoleIn()`），请将 `HasClearance` trait 添加到 `App\Models\User` 中。它已经包含了 Spatie 的 `HasRoles`——一行代码即可替代两者：

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance; // 包含 HasRoles 以及所有上下文授权方法
}
```

::: tip 已经在使用 `use HasRoles`？
将其替换为 `use HasClearance`，或者两者都保留——PHP 会自动对 trait 组合去重，不会产生冲突。
:::

如果不添加此 trait，`$user->canIn()` 及相关方法将不可用，但 Blade 指令（`@canin`、`@hasrolein`）和 `ContextService` 仍会照常工作——它们并不依赖此 trait。详情参见[上下文授权](/zh/features/contextual-authorization)。

## 下一步

继续阅读[快速开始](/zh/guide/quick-start)，挂载面板并开始管理权限。
