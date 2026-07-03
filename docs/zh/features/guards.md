# 看守器

看守器面板将认证看守器以数据库记录的形式进行管理，因此无需编辑 `config/auth.php` 并重新部署即可添加或更改看守器。

## 该面板的功能

- **创建/编辑/删除**看守器，每个看守器都有一个 `name`、`driver` 和 `provider`。

<Screenshot src="/screenshots/guards-page.png" alt="Clearance 看守器面板列出数据库支持的看守器" caption="看守器面板列出每一个数据库支持的看守器及其驱动程序和提供者。" />

## 看守器的应用方式

从面板管理的看守器会在应用启动时注入到 `auth.guards` 中，因此在任何请求运行之前，Laravel 的认证系统就已经能识别它们——无需手动编辑 `config/auth.php`。

## 白名单驱动程序

只接受白名单中的驱动程序：

```php
// config/clearance.php
'allowed_guard_drivers' => ['session', 'token', 'jwt', 'passport', 'sanctum'],
```

::: warning 未列入白名单的驱动程序会被跳过，而不是报错拒绝
一个驱动程序不在 `allowed_guard_drivers` 中的看守器，会在启动时被跳过（并记录一条日志警告），而不会破坏认证配置。如果你创建的某个看守器没有生效，请先检查其驱动程序是否在白名单内。
:::

## 访问控制

读访问需要 `clearance-access`。创建、编辑或删除看守器需要 `clearance-guards-write`：

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-guards-write');
```

## 路由与嵌入式组件

| URI | 路由名称 | 说明 |
|---|---|---|
| `/clearance/guards` | `clearance.guards` | 看守器管理（创建/编辑/删除） |

```blade
<livewire:clearance::guards.guard-manager />
```

## 数据库

| 数据表 | 关键列 |
|---|---|
| `clr_guards` | `name`（唯一）、`driver`、`provider` |

## 下一步

- [配置参考](/zh/guide/configuration)——完整的 `allowed_guard_drivers` 和 `guards` 配置键。
- [Facade 参考](/zh/api/facade)——`Clearance::guards()` 返回所有受管理认证看守器的名称。
