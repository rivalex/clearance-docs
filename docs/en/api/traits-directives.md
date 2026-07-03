# Traits & Directives

Reference for the traits, concern, contract, and Blade directives that make up Clearance's developer-facing surface outside of the facade and services.

## `HasClearance`

`Rivalex\Clearance\Traits\HasClearance` — add to your `User` model.

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance;
}
```

It includes Spatie's `HasRoles` internally, so this one trait replaces both — do not add `Spatie\Permission\Traits\HasRoles` separately.

It adds the following instance methods:

```php
$user->canIn('orders-create', $store);            // bool
$user->canIn('orders-create', $store, 'api');     // optional guard filter
$user->hasPermissionIn('orders-create', $store);  // alias of canIn()
$user->hasRoleIn('manager', $store);               // bool
$user->hasRoleIn('manager', $store, 'web');         // optional guard filter
```

| Method | Returns | Description |
|---|---|---|
| `canIn(string $permission, $context, ?string $guard = null)` | `bool` | Permission check scoped to a context instance. |
| `hasPermissionIn(string $permission, $context, ?string $guard = null)` | `bool` | Alias of `canIn()`. |
| `hasRoleIn(string $role, $context, ?string $guard = null)` | `bool` | Role check scoped to a context instance. |

::: tip
Without this trait, these instance methods are unavailable — but the Blade directives and `ContextService` keep working regardless. Neither depends on the trait.
:::

## `HasPermissionGroups`

`Rivalex\Clearance\Concerns\HasPermissionGroups` — use when you need a custom `Permission` model (configured via `config('permission.models.permission')`) instead of Clearance's own `Rivalex\Clearance\Models\Permission`.

Apply it to keep the grouped-UI accessors that back the Permissions panel working on your custom model:

```php
use Rivalex\Clearance\Concerns\HasPermissionGroups;

class Permission extends \Spatie\Permission\Models\Permission
{
    use HasPermissionGroups;
}
```

It provides these accessors:

| Accessor / method | Description |
|---|---|
| `permission_group` | The group prefix before the naming separator (e.g. `orders` in `orders-create`). |
| `group_string` | The group as a display string. |
| `abilities()` | The set of abilities/actions defined for the permission's group. |
| `colorForAbility()` | A color for the permission's ability, used by the panel UI. |

See [Permissions](/en/features/permissions.md) for how the naming convention and grouping are used in the panel.

## `ClearanceContextable`

`Rivalex\Clearance\Contracts\ClearanceContextable` — implement on a context model (e.g. your `Store` model) to control how it's labeled in the panel.

```php
interface ClearanceContextable
{
    public function clearanceLabel(): string;
}
```

If a context model does not implement this contract, the panel falls back to `contextual_models.<FQCN>.label_attribute` (default `name`) from `config/clearance.php`. See [Configuration](/en/guide/configuration.md).

## Blade directives

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

| Directive | Equivalent check |
|---|---|
| `@canin($permission, $context)` | `ContextService::canIn()` |
| `@hasrolein($role, $context)` | `ContextService::hasRoleIn()` |

::: tip
Both directives resolve safely for a guest user — they return `false` / skip the block and never throw. They also don't require the `HasClearance` trait; they resolve through the same underlying `ContextService` used by the facade.
:::

## See also

- [Facade](/en/api/facade.md) — the static-call equivalent of these instance methods.
- [Services](/en/api/services.md) — `ContextService` internals and the 3-source resolution precedence.
