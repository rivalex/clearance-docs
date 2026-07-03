# 上下文授权

Clearance 用上下文限定的检查扩展了 Spatie 的全局授权模型。用户的角色可以被分配到一个特定上下文内（例如一个 Store、Tenant 或 Project），并且在该范围之外不可见。这正是[用户模块](/zh/features/users)以及上下文[角色](/zh/features/roles#作用域-全局-vs-上下文)背后的核心机制。

## 在 User 模型上

当你的 `User` 模型使用了 `HasClearance` trait 时可用（参见[安装](/zh/guide/installation#在你的-user-模型中添加-hasclearance)）：

```php
// Does the user have this permission in this context?
$user->canIn('orders-create', $store);

// Alias - mirrors Spatie's hasPermissionTo() naming
$user->hasPermissionIn('orders-create', $store);

// Does the user hold this role in this context?
$user->hasRoleIn('manager', $store);

// Optionally filter by guard
$user->canIn('orders-create', $store, 'api');
$user->hasRoleIn('manager', $store, 'web');
```

## 在 Blade 视图中

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

::: tip 设计上对访客安全
两个指令对访客用户都会安全地解析（返回 `false`）——它们从不抛出异常。你可以在授权状态尚未确定就渲染的视图中使用 `@canin`/`@hasrolein`，而无需将它们包裹在 `@auth` 检查中。
:::

## 通过 ContextService

用于控制器、策略或任务队列（job）中——此时 `User` 模型可能并未使用 `HasClearance` trait：

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

## 通过 Clearance facade

`Clearance` facade 委托给同一个服务——一个便捷的全局入口点：

```php
Clearance::canIn($user, 'orders-create', $store);
Clearance::resolveFor($user, $store);
Clearance::guards(); // names of all managed authentication guards
```

完整方法列表请参见 [Facade 参考](/zh/api/facade)。

## 解析机制

`resolveFor()` 按以下优先级顺序合并三个来源：

1. **上下文角色授权**——来自用户在此上下文中专门持有的角色所授予的权限（`clr_role_ctx`）。
2. **全局角色授权**——来自用户全局分配的 Spatie 角色所授予的权限（因此 `super_admin` 及其他全局角色对任意上下文的 `canIn()` 检查都能满足）。
3. **按上下文的覆盖**（`clr_ctx_overrides`）——`forced_on` 在上述基础上追加一个权限；`forced_off` 则移除它，即使该权限是由全局角色授予的也是如此。

::: danger 拒绝始终优先生效
如果某个权限在给定上下文中对某个用户被 `forced_off`，那么该拒绝会覆盖所有其他来源——包括全局角色授权和上下文角色授权。除了移除该覆盖本身之外，没有任何方法能在该上下文中重新授予一个被 `forced_off` 的权限。在设计你的覆盖策略时请牢记这一点：`forced_off` 是系统中最强的信号。
:::

## 数据库

| 数据表 | 关键列 |
|---|---|
| `clr_role_ctx` | `user_id`、`role_id`（外键 -> `roles`）、`context_type`、`context_id`——按 `(user, role, context_type, context_id)` 联合唯一 |
| `clr_ctx_overrides` | `user_id`、`role_id`（外键 -> `roles`）、`permission_id`（外键 -> `permissions`）、`context_type`、`context_id`、`type`（`forced_on`\|`forced_off`） |

## 下一步

- [用户](/zh/features/users)——用于分配上下文角色和覆盖的面板 UI。
- [角色](/zh/features/roles#作用域-全局-vs-上下文)——将一个角色声明为 `contextual`，以及其允许的 `context_types`。
- [Traits & Directives 参考](/zh/api/traits-directives)——完整的 `HasClearance` trait 和 Blade 指令签名。
- [服务参考](/zh/api/services)——完整的 `ContextService` API。
