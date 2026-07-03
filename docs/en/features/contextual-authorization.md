# Contextual Authorization

Clearance extends Spatie's global authorization model with context-scoped checks. A user's roles can be assigned within a specific context (e.g. a Store, Tenant, or Project) and are invisible outside that scope. This is the core mechanic behind the [Users module](/en/features/users) and contextual [Roles](/en/features/roles#scope-global-vs-contextual).

## On the User model

Available when your `User` model uses the `HasClearance` trait (see [Installation](/en/guide/installation#add-hasclearance-to-your-user-model)):

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

## In Blade views

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

::: tip Guest-safe by design
Both directives resolve safely (return `false`) for a guest user - they never throw. You can use `@canin`/`@hasrolein` in views rendered before authentication is guaranteed without wrapping them in an `@auth` check.
:::

## Via ContextService

For use in controllers, policies, or jobs where the `User` model may not have the `HasClearance` trait:

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

## Via the Clearance facade

The `Clearance` facade delegates to the same service - a convenient global entry point:

```php
Clearance::canIn($user, 'orders-create', $store);
Clearance::resolveFor($user, $store);
Clearance::guards(); // names of all managed authentication guards
```

See the [Facade reference](/en/api/facade) for the full method list.

## How resolution works

`resolveFor()` merges three sources, in this precedence order:

1. **Contextual role grants** - permissions from roles the user holds specifically within this context (`clr_role_ctx`).
2. **Global role grants** - permissions from the user's globally-assigned Spatie roles (so `super_admin` and other global roles satisfy `canIn()` for any context).
3. **Per-context overrides** (`clr_ctx_overrides`) - `forced_on` adds a permission on top of the above; `forced_off` removes it, even if the permission was granted by a global role.

::: danger A deny always wins
If a permission is `forced_off` for a user in a given context, that denial overrides every other source - including a global role grant and a contextual role grant. There is no way to re-grant a `forced_off` permission for that context other than removing the override itself. Design your override policy with this in mind: `forced_off` is the strongest signal in the system.
:::

## Database

| Table | Key columns |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - unique per `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## Next steps

- [Users](/en/features/users) - the panel UI for assigning contextual roles and overrides.
- [Roles](/en/features/roles#scope-global-vs-contextual) - declaring a role as `contextual` and its allowed `context_types`.
- [Traits & Directives reference](/en/api/traits-directives) - full `HasClearance` trait and Blade directive signatures.
- [Services reference](/en/api/services) - full `ContextService` API.
