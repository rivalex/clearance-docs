# Roles

The Roles panel is a full CRUD interface over Spatie's `Role` model, extended with two Clearance-specific concepts: **scope** (global vs. contextual) and **ceiling** (a parent role that bounds a child's permissions).

## What the panel does

- **Create / edit / delete** roles.
- **Guard-scoped permission assignment** - assign permissions to a role, filtered to the role's guard.
- **Scope** selection - `global` or `contextual`.
- **Ceiling** configuration - set or remove a parent role.

<Screenshot src="/screenshots/roles-page.png" alt="Clearance Roles panel listing roles with scope and ceiling" caption="The Roles panel lists every role with its scope and, if set, its ceiling parent." />

## Scope: global vs. contextual

Every role declares a scope at creation time:

- **`global`** - assigned directly to a user via `$user->assignRole()`, applies everywhere.
- **`contextual`** - bound to one or more model types (`context_types`), assignable only within a specific context instance via `UserRoleContext`. See [Contextual Authorization](/en/features/contextual-authorization) and [Users](/en/features/users#contextual-roles-under-the-hood).

Roles without an explicit `RoleMeta` row default to `global` for backwards compatibility.

## Ceiling roles

A role can declare a ceiling - a parent role whose permission set acts as an upper bound:

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
```

<Screenshot src="/screenshots/role-form-ceiling.png" alt="Role form with a ceiling parent role selected" caption="Setting a ceiling parent in the Role form (RoleForm)." />

Rules enforced automatically on every save:

- The child can only ever hold permissions the parent also holds. Any excess is silently trimmed on `setParent()` and on every subsequent permission sync - no exception, no error message, just enforced silently.
- A role that already acts as a parent cannot become a child (`ClearanceScopeViolationException`).
- A role that is already a child cannot itself act as a parent (`ClearanceScopeViolationException`).
- Removing permissions from a parent cascades the removal to every child automatically.

::: warning A ceiling only trims, it never grants
Setting a permissive parent grants the child nothing by itself. The ceiling is strictly an upper bound - it never adds permissions to the child, it can only remove them.
:::

This is configured per-role in the Roles panel (`RoleForm`), or programmatically via `RoleService::setParent()` / `RoleService::removeParent()`.

## Access control

Read access requires `clearance-access`. Creating, editing, or deleting a role - including its permission assignments, scope, and ceiling - requires `clearance-roles-write`:

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

## Route and embedded component

| URI | Route name | Description |
|---|---|---|
| `/clearance/roles` | `clearance.roles` | Role CRUD with guard-scoped permission assignment, scope (global/contextual), and ceiling |

```blade
<livewire:clearance::roles.role-manager />
```

## Database

Role metadata (scope, ceiling, lock state) lives in `clr_role_meta`, never in Spatie's `roles` table:

| Table | Key columns |
|---|---|
| `clr_role_meta` | `role_id` (unique, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (nullable FK -> `roles`, `ON DELETE SET NULL`) |

## Next steps

- [Contextual Authorization](/en/features/contextual-authorization) - how `scope=contextual` roles are checked at runtime.
- [Users](/en/features/users) - assigning roles to a specific user, globally or per context.
- [Services reference](/en/api/services) - full `RoleService` API.
