# Permissions

The Permissions panel is a full CRUD interface over Spatie's `Permission` model. Clearance never introduces its own permission table - every permission you create here is a regular Spatie permission, usable anywhere Spatie's API is used (`$user->can()`, `Gate::allows()`, policies, `@can`).

## What the panel does

- **Create** - add a new permission, validated against the [naming convention](#permission-naming-convention) (unless disabled) and scoped to a guard.
- **Edit** - rename a permission or change its guard.
- **Delete** - remove a permission and its role/user assignments (standard Spatie cascade behavior).

<Screenshot src="/screenshots/permissions-page.png" alt="Clearance Permissions panel listing permissions grouped by naming prefix" caption="The Permissions panel lists every Spatie permission, grouped by naming prefix." />

<Screenshot src="/screenshots/permission-form-modal.png" alt="Permission create/edit form with naming convention validation" caption="The permission form validates the group-action naming convention before saving." />

## Permission Naming Convention

Permissions must follow the `group-action` format:

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

The group prefix (`orders` in `orders-create`) is what the [Dashboard](/en/features/dashboard) uses to count distinct permission groups.

::: tip Disabling enforcement
Some teams migrate from a different naming scheme. Disable the check per environment:

```php
// config/clearance.php
'enforce_naming_convention' => false,
```
:::

## Access control

Read access to the panel (viewing the permission list) requires `clearance-access`. Creating, editing, or deleting a permission additionally requires `clearance-permissions-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-permissions-write');
```

::: warning `clearance-*` permissions are reserved
The `clearance-*` permissions themselves (`clearance-access`, `clearance-permissions-write`, etc.) cannot be created, renamed, or deleted through this panel - even by a `super_admin` actor. They're managed exclusively by the package's own install flow. See [Super Admin](/en/features/super-admin#reserved-clearance-permissions).
:::

## Route and embedded component

| URI | Route name | Description |
|---|---|---|
| `/clearance/permissions` | `clearance.permissions` | Permission CRUD with group-prefix naming validation |

```blade
<livewire:clearance::permissions.permission-manager />
```

## Next steps

- [Roles](/en/features/roles) - assign permissions to a role, guard-scoped.
- [Super Admin](/en/features/super-admin) - the full fine-grained write-permission table.
- [Configuration reference](/en/guide/configuration) - `enforce_naming_convention`, `naming_separator`.
