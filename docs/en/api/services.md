# Services

All Clearance services live in `src/Services/` and are resolved from the container, e.g. `app(ContextService::class)`. They are the layer to reach for from controllers, policies, and jobs — anywhere the `User` model may not use the `HasClearance` trait.

## `ContextService`

`Rivalex\Clearance\Services\ContextService` (`src/Services/ContextService.php`) is the engine behind contextual authorization. The [`Clearance` facade](/en/api/facade.md) and the `HasClearance` trait both delegate to it.

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

`resolveFor()` builds its result using a 3-source precedence:

1. Contextual role grants
2. Global role grants
3. Per-context overrides (`forced_on` adds, `forced_off` always wins)

See [Contextual Authorization](/en/features/contextual-authorization.md) for the conceptual walkthrough of this precedence.

## `RoleService`

`Rivalex\Clearance\Services\RoleService` (`src/Services/RoleService.php`) manages ceiling roles.

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
app(RoleService::class)->removeParent($childRole);
```

| Method | Description |
|---|---|
| `setParent($childRole, $parentRole)` | Sets a ceiling: excess permissions on `$childRole` beyond the parent's set are silently trimmed. Throws `ClearanceScopeViolationException` if the child is already a parent, or the parent is already a child. |
| `removeParent($childRole)` | Removes the ceiling relationship. |

::: warning
A ceiling only trims — it never grants. A permissive parent role does not, by itself, give the child any permissions. Removing a permission from a parent role cascades that removal to every child role automatically.
:::

See [Roles](/en/features/roles.md) for the ceiling-role concept in more depth.

## `PermissionService`

`Rivalex\Clearance\Services\PermissionService` (`src/Services/PermissionService.php`) backs the Permissions panel's CRUD.

It enforces the `group-action` naming convention, controlled by the `enforce_naming_convention` and `naming_separator` config keys, and protects the `clearance-*` permission namespace from being created, renamed, or deleted outside the install flow.

## `GuardService`

`Rivalex\Clearance\Services\GuardService` (`src/Services/GuardService.php`) backs the Guards panel's CRUD.

It injects DB-backed guards into `auth.guards` at boot, filtered by the `allowed_guard_drivers` config key — an unlisted driver is skipped with a log warning rather than corrupting the auth config.

## `MetaService`

`Rivalex\Clearance\Services\MetaService` (`src/Services/MetaService.php`) reads and writes `clr_meta` (display name, description, sanitized SVG icon, color) for a role or guard subject. It backs the Settings panel's display-metadata editor.

SVG icons pass through `Rivalex\Clearance\Support\SvgSanitizer` (an allow-list sanitizer) both before storage and at render time.

## `UserClearanceService`

`Rivalex\Clearance\Services\UserClearanceService` (`src/Services/UserClearanceService.php`) backs the Users module panel:

- Global role assignment
- Contextual role assignment (via `UserRoleContext`)
- Per-context permission overrides (via `UserContextPermissionOverride`, `forced_on` / `forced_off`)
- Removing assignments, which cascades override deletion

It enforces a privilege ceiling: a non-`super_admin` actor cannot modify their own assignments, and cannot grant a permission they don't themselves hold. See [Super Admin](/en/features/super-admin.md) and [Users](/en/features/users.md).

## Exceptions

All in `src/Exceptions/`:

| Exception | Thrown when |
|---|---|
| `ClearanceConfigException` | An invalid or misconfigured `config/clearance.php` value is read (e.g. a bad `naming_separator`). |
| `ClearanceNamingException` | A permission name violates the `group-action` naming convention while enforcement is on. |
| `ClearanceProtectedResourceException` | An attempt is made to modify a protected `clearance-*` permission, or a locked resource, outside the install flow. |
| `ClearanceScopeViolationException` | An invalid ceiling-role relationship (a parent trying to become a child, or vice versa), or a scope mismatch. |

## See also

- [Facade](/en/api/facade.md) — the static-call wrapper around `ContextService`.
- [Traits & Directives](/en/api/traits-directives.md) — instance-method and Blade-directive equivalents.
- [Artisan Commands](/en/api/artisan-commands.md) — `clearance:install` and `clearance:backfill`, which seed the data these services operate on.
