# 配置

Clearance 内置了合理的默认值，因此[安装](/zh/guide/installation)后即可开箱使用。当你需要自定义路由前缀、中间件、上下文模型或其他任何设置时，再发布配置文件。

```bash
php artisan vendor:publish --tag=clearance-config
```

这会发布 `config/clearance.php`。

## 配置参考

这是 `config/clearance.php` 中每一个键的权威参考。本文档的其他页面都会链接回这里，而不是重复这张表格。

| 键 | 默认值 | 用途 |
|---|---|---|
| `route_prefix` | `'clearance'` | 所有面板路由使用的 URI 前缀。 |
| `middleware` | `['web', 'auth']` | 在 `clearance.access` 之前应用于所有面板路由。 |
| `access_permission` | `'clearance-access'` | 访问中间件所检查的权限。 |
| `user_model` | `null` | 默认取自 `auth.providers.users.model`。 |
| `modules.users` | `false` | 用户模块的总开关（路由 + Livewire 组件）。 |
| `contextual_models` | `[]` | `FQCN => ['label', 'icon', 'label_attribute', 'searchable']`——可以作为角色绑定上下文的模型。 |
| `enforce_naming_convention` | `true` | 强制执行 `group-action` 权限命名约定。 |
| `naming_separator` | `'-'` | 分隔符——仅限 `'-'` 或 `'_'`。 |
| `guards` | `[]` | 覆盖从 `config/auth.php` 自动检测到的看守器。为空 = 自动检测全部。 |
| `allowed_guard_drivers` | `['session', 'token', 'jwt', 'passport', 'sanctum']` | 将数据库看守器注入 `auth.guards` 时所接受的驱动程序。 |
| `layout` | `null` | 全页组件所使用的 Blade 布局。`null` = 使用宿主应用的默认布局（`config('livewire.layout')`）。 |
| `auto_assign_default_role` | `false` | 在 `Registered` 事件时自动分配在设置中配置的默认角色。 |
| `super_admin_gate_bypass` | `false` | 可选启用：`super_admin` 将绕过整个应用范围内的所有 Gate 检查。 |
| `ui.flux_pro` | `null` | `null` = 通过 `Flux::pro()` 自动检测。 |

## 权限命名约定

权限必须遵循 `group-action` 格式：

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

通过 `enforce_naming_convention` 按环境禁用此约束：

```php
'enforce_naming_convention' => false,
```

::: warning 分隔符在启动时会被校验
`naming_separator` 的值是这份配置中唯一会进入原始 SQL 片段（用于对权限进行分组/排序）的部分，因此只接受 `-` 或 `_`。
:::

## 自定义中间件

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

无论前缀或中间件如何变化，路由名称始终保持稳定——完整的路由表请参见[快速开始](/zh/guide/quick-start)。

## 相关链接

- [快速开始](/zh/guide/quick-start)——在应用这些设置后挂载面板
- [发布资源](/zh/guide/publishing)——发布翻译文件，以及理解 `layout` 键
- [看守器](/zh/features/guards)——`guards` 和 `allowed_guard_drivers` 的使用方式
- [超级管理员](/zh/features/super-admin)——`super_admin_gate_bypass` 的工作原理
