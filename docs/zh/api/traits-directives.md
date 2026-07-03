# Trait 与指令

关于构成 Clearance 面向开发者的接口（在 facade 和服务之外）的 trait、concern、contract 以及 Blade 指令的参考。

## `HasClearance`

`Rivalex\Clearance\Traits\HasClearance`——添加到你的 `User` 模型中。

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance;
}
```

它在内部包含了 Spatie 的 `HasRoles`，因此这一个 trait 就能替代两者——不要再单独添加 `Spatie\Permission\Traits\HasRoles`。

它新增了以下实例方法：

```php
$user->canIn('orders-create', $store);            // bool
$user->canIn('orders-create', $store, 'api');     // optional guard filter
$user->hasPermissionIn('orders-create', $store);  // alias of canIn()
$user->hasRoleIn('manager', $store);               // bool
$user->hasRoleIn('manager', $store, 'web');         // optional guard filter
```

| 方法 | 返回值 | 说明 |
|---|---|---|
| `canIn(string $permission, $context, ?string $guard = null)` | `bool` | 限定在某个上下文实例范围内的权限检查。 |
| `hasPermissionIn(string $permission, $context, ?string $guard = null)` | `bool` | `canIn()` 的别名。 |
| `hasRoleIn(string $role, $context, ?string $guard = null)` | `bool` | 限定在某个上下文实例范围内的角色检查。 |

::: tip
如果不添加此 trait，这些实例方法将不可用——但 Blade 指令和 `ContextService` 仍会照常工作。两者都不依赖此 trait。
:::

## `HasPermissionGroups`

`Rivalex\Clearance\Concerns\HasPermissionGroups`——当你需要使用自定义的 `Permission` 模型（通过 `config('permission.models.permission')` 配置），而不是 Clearance 自身的 `Rivalex\Clearance\Models\Permission` 时使用。

将其应用到你的自定义模型上，以保持支撑权限面板的分组 UI 访问器正常工作：

```php
use Rivalex\Clearance\Concerns\HasPermissionGroups;

class Permission extends \Spatie\Permission\Models\Permission
{
    use HasPermissionGroups;
}
```

它提供以下访问器：

| 访问器/方法 | 说明 |
|---|---|
| `permission_group` | 命名分隔符之前的组前缀（例如 `orders-create` 中的 `orders`）。 |
| `group_string` | 以显示字符串形式呈现的组。 |
| `abilities()` | 为该权限所属组定义的一组权限项/操作。 |
| `colorForAbility()` | 该权限对应操作的一个颜色，用于面板 UI。 |

命名约定和分组在面板中的使用方式，请参见[权限](/zh/features/permissions.md)。

## `ClearanceContextable`

`Rivalex\Clearance\Contracts\ClearanceContextable`——在一个上下文模型（例如你的 `Store` 模型）上实现，以控制它在面板中如何被标注。

```php
interface ClearanceContextable
{
    public function clearanceLabel(): string;
}
```

如果一个上下文模型没有实现该 contract，面板会回退使用 `config/clearance.php` 中的 `contextual_models.<FQCN>.label_attribute`（默认为 `name`）。参见[配置](/zh/guide/configuration.md)。

## Blade 指令

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

| 指令 | 等效检查 |
|---|---|
| `@canin($permission, $context)` | `ContextService::canIn()` |
| `@hasrolein($role, $context)` | `ContextService::hasRoleIn()` |

::: tip
两个指令对访客用户都会安全地解析——它们会返回 `false`/跳过该代码块，从不抛出异常。它们也不要求 `HasClearance` trait；它们通过 facade 所使用的同一个底层 `ContextService` 进行解析。
:::

## 另请参阅

- [Facade](/zh/api/facade.md)——这些实例方法对应的静态调用等价形式。
- [服务](/zh/api/services.md)——`ContextService` 的内部实现，以及三来源解析优先级。
