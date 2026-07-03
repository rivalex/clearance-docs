# Super Admin

The `super_admin` role is created automatically by `clearance:install` and kept in sync with all `clearance-*` permissions.

```bash
# Install and make user ID 1 a super admin
php artisan clearance:install --user=1
```

See the [Installation guide](/en/guide/installation) and [Artisan commands reference](/en/api/artisan-commands) for the full installer flag list.

## Gate bypass (opt-in)

`super_admin_gate_bypass` controls whether the `super_admin` role bypasses all Laravel `can()` / `Gate::allows()` / policy checks globally, via a `Gate::before()` hook. Default: `false`. The bypass is opt-in. To enable it:

```php
// config/clearance.php
'super_admin_gate_bypass' => true,
```

::: danger Application-wide, not panel-scoped
When enabled, any user holding `super_admin` skips **all** Gate checks application-wide - not only Clearance panel checks. This affects every `can()`, `Gate::allows()`, and policy check in your application, not just the ones Clearance defines. For a narrower scope, leave it off and grant permissions explicitly instead.
:::

## Alias detection

If your database already contains a role named `super_admin`, `superadmin`, `super-admin`, `root`, or `owner`, the installer detects it and prompts you interactively to promote it or create a separate `super_admin`. Use `--super-admin-role=NAME` to skip the prompt:

```bash
php artisan clearance:install --super-admin-role=owner
```

::: tip Promotion is non-destructive
Promotion renames the role in place - all existing `model_has_roles` assignments are preserved via the FK on `role_id`. No user loses their existing role assignment during promotion.
:::

## Panel Access & Fine-grained write permissions

Navigate to `/clearance` after installation. Read access to the whole panel requires the `clearance-access` permission, checked via `can()`, never `hasRole()`:

```php
$user->givePermissionTo('clearance-access');
```

Write operations on each section are gated by a dedicated, delegable permission - all created by `clearance:install` and assigned to `super_admin`:

| Permission | Controls |
|---|---|
| `clearance-permissions-write` | Create / edit / delete permissions |
| `clearance-roles-write` | Create / edit / delete roles and their permission assignments |
| `clearance-guards-write` | Create / edit / delete guards |
| `clearance-settings-write` | Edit display metadata, icons, colors, default role |
| `clearance-users-write` | Assign / remove roles for a specific user |

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

::: tip Granting narrow access
Combine `clearance-access` with exactly one `clearance-{section}-write` permission to hand a user (say, a team lead) write access to a single panel section - roles, for instance - without exposing settings, guards, or user management.
:::

If a fine-grained permission row doesn't exist at all in the database (a legacy pre-seeding state), the check falls back to `clearance-access` for backwards compatibility. On any instance that has run `clearance:install`, this fallback never activates.

## Reserved `clearance-*` permissions

`clearance-*` permissions are reserved: they cannot be created, renamed, deleted, or added to any role except through the package's own install flow - even by a `super_admin` actor for the `super_admin` role itself.

## Next steps

- [Permissions](/en/features/permissions) - the general permission CRUD panel (excludes `clearance-*`).
- [Roles](/en/features/roles) - assigning `clearance-*-write` permissions to a custom role.
- [Security](/en/guide/security) - the full privilege-escalation and ceiling model.
