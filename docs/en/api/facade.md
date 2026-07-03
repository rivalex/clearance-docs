# Facade

The `Clearance` facade (`Rivalex\Clearance\Facades\Clearance`) exposes the package's contextual authorization API as static-style calls. It is backed by `src/Clearance.php`, which delegates every call to [`ContextService`](/en/api/services.md#contextservice).

```php
use Rivalex\Clearance\Facades\Clearance;
```

::: tip
The Blade directives and `ContextService` work without any change to your `User` model. The facade is a convenience wrapper around the same engine — see [Contextual Authorization](/en/features/contextual-authorization.md) for the concept behind context-scoped checks.
:::

## `canIn()`

Check whether a user holds a permission scoped to a context instance.

```php
Clearance::canIn($user, 'orders-create', $store);      // bool
Clearance::canIn($user, 'orders-create', $store, 'api'); // optional 3rd arg: guard name filter
```

| Parameter | Type | Description |
|---|---|---|
| `$user` | `Authenticatable` | The user to check. |
| `string $permission` | `string` | Permission name, e.g. `orders-create`. |
| `$context` | model instance | The context the permission is scoped to, e.g. a `Store`. |
| `?string $guard` | `string\|null` | Optional guard name filter. |

Returns `bool`.

## `hasPermissionIn()`

Alias of [`canIn()`](#canin), named to mirror Spatie's `hasPermissionTo()` convention.

```php
Clearance::hasPermissionIn($user, 'orders-create', $store);
```

Returns `bool`.

## `hasRoleIn()`

Check whether a user holds a role scoped to a context instance.

```php
Clearance::hasRoleIn($user, 'manager', $store);
```

Returns `bool`.

## `resolveFor()`

Resolve every effective permission name a user holds within a given context.

```php
Clearance::resolveFor($user, $store); // Collection<string>
```

Returns a `Collection<string>` of permission names. See [`ContextService::resolveFor()`](/en/api/services.md#contextservice) for the 3-source precedence rules used to build this list.

## `guards()`

List the names of all authentication guards managed by Clearance.

```php
Clearance::guards(); // Collection<string>
```

Returns a `Collection<string>`. Backed by [`GuardService`](/en/api/services.md#guardservice). See the [Guards feature page](/en/features/guards.md) for how guards are configured and injected into `auth.guards`.

## See also

- [Traits & Directives](/en/api/traits-directives.md) — the equivalent instance methods via `HasClearance`, and the guest-safe Blade directives.
- [Services](/en/api/services.md) — the underlying `ContextService` and related services.
