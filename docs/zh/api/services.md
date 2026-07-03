# 服务

Clearance 的所有服务都位于 `src/Services/` 中，并从容器中解析，例如 `app(ContextService::class)`。它们是从控制器、策略和任务队列（job）中调用的入口层——适用于任何 `User` 模型可能未使用 `HasClearance` trait 的场景。

## `ContextService`

`Rivalex\Clearance\Services\ContextService`（`src/Services/ContextService.php`）是上下文授权背后的引擎。[`Clearance` facade](/zh/api/facade.md) 和 `HasClearance` trait 都委托给它。

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

`resolveFor()` 使用三来源优先级构建其结果：

1. 上下文角色授权
2. 全局角色授权
3. 按上下文的覆盖（`forced_on` 追加，`forced_off` 始终优先生效）

关于此优先级的概念性讲解，请参见[上下文授权](/zh/features/contextual-authorization.md)。

## `RoleService`

`Rivalex\Clearance\Services\RoleService`（`src/Services/RoleService.php`）管理上限角色。

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
app(RoleService::class)->removeParent($childRole);
```

| 方法 | 说明 |
|---|---|
| `setParent($childRole, $parentRole)` | 设置一个上限：`$childRole` 上超出父角色权限集合的部分会被默默裁剪。如果子角色已经是父角色，或者父角色已经是子角色，则抛出 `ClearanceScopeViolationException`。 |
| `removeParent($childRole)` | 移除上限关系。 |

::: warning
上限只会裁剪权限——从不授予权限。一个权限宽松的父角色本身不会给子角色带来任何权限。从父角色移除一个权限会自动级联到每一个子角色。
:::

关于上限角色概念的更深入介绍，请参见[角色](/zh/features/roles.md)。

## `PermissionService`

`Rivalex\Clearance\Services\PermissionService`（`src/Services/PermissionService.php`）支撑权限面板的 CRUD。

它强制执行 `group-action` 命名约定（由 `enforce_naming_convention` 和 `naming_separator` 配置键控制），并保护 `clearance-*` 权限命名空间，使其无法在安装流程之外被创建、重命名或删除。

## `GuardService`

`Rivalex\Clearance\Services\GuardService`（`src/Services/GuardService.php`）支撑看守器面板的 CRUD。

它会在应用启动时将数据库支持的看守器注入到 `auth.guards` 中，并按 `allowed_guard_drivers` 配置键进行过滤——一个未列入白名单的驱动程序会被跳过，并记录一条日志警告，而不会破坏认证配置。

## `MetaService`

`Rivalex\Clearance\Services\MetaService`（`src/Services/MetaService.php`）读写 `clr_meta`（角色或看守器主体的显示名称、描述、经过净化的 SVG 图标、颜色）。它支撑设置面板的显示元数据编辑器。

SVG 图标在存储前以及每次渲染时都会经过 `Rivalex\Clearance\Support\SvgSanitizer`（一个白名单净化器）处理。

## `UserClearanceService`

`Rivalex\Clearance\Services\UserClearanceService`（`src/Services/UserClearanceService.php`）支撑用户模块面板：

- 全局角色分配
- 上下文角色分配（通过 `UserRoleContext`）
- 按上下文的权限覆盖（通过 `UserContextPermissionOverride`，`forced_on`/`forced_off`）
- 移除分配，并级联删除覆盖

它强制执行一个权限提升上限：非 `super_admin` 的操作者无法修改自己的分配，也无法授予自己都不持有的权限。参见[超级管理员](/zh/features/super-admin.md)和[用户](/zh/features/users.md)。

## 异常

全部位于 `src/Exceptions/` 中：

| 异常 | 抛出时机 |
|---|---|
| `ClearanceConfigException` | 读取到一个无效或配置错误的 `config/clearance.php` 值时（例如一个不合法的 `naming_separator`）。 |
| `ClearanceNamingException` | 在启用强制校验的情况下，一个权限名称违反了 `group-action` 命名约定时。 |
| `ClearanceProtectedResourceException` | 在安装流程之外，尝试修改一个受保护的 `clearance-*` 权限，或一个被锁定的资源时。 |
| `ClearanceScopeViolationException` | 出现一个无效的上限角色关系（一个父角色试图成为子角色，反之亦然），或作用域不匹配时。 |

## 另请参阅

- [Facade](/zh/api/facade.md)——围绕 `ContextService` 的静态调用封装。
- [Trait 与指令](/zh/api/traits-directives.md)——实例方法与 Blade 指令的等价形式。
- [Artisan 命令](/zh/api/artisan-commands.md)——`clearance:install` 和 `clearance:backfill`，它们为这些服务所操作的数据播种。
