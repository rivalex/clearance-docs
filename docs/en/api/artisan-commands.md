# Artisan Commands

Clearance ships two Artisan commands: `clearance:install` for first-time setup, and `clearance:backfill` for seeding defaults on existing installs. See the [Installation guide](/en/guide/installation.md) for the end-to-end setup walkthrough.

## `clearance:install`

```bash
php artisan clearance:install
php artisan clearance:install --user=1
php artisan clearance:install --role=editor
php artisan clearance:install --super-admin-role=owner
php artisan clearance:install --force
```

| Flag | Description |
|---|---|
| `--user=ID` | Assign the `super_admin` role to a user after install. |
| `--role=NAME` | Assign `clearance-access` to a role (created if absent). |
| `--super-admin-role=NAME` | Promote an existing role to `super_admin` (skips the interactive prompt). |
| `--force` | Re-run even if already installed. |

### What it does, in order

1. Publishes `config/clearance.php`.
2. Publishes Clearance's migrations.
3. Detects Spatie Permission tables — if the `roles` table is absent, publishes and runs Spatie's migrations automatically first.
4. Runs all pending migrations.
5. Creates `clearance-access` and all 5 fine-grained capability permissions: `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`.
6. Creates the `super_admin` role with all `clearance-*` permissions assigned. This step is additive — it never removes existing permissions on re-run.
7. Writes `storage/.clearance-installed` as an idempotency marker.

### Alias detection

If the database already contains a role named `super_admin`, `superadmin`, `super-admin`, `root`, or `owner`, the installer detects it and prompts interactively to either promote it or create a separate `super_admin` role. Use `--super-admin-role=NAME` to skip the prompt non-interactively.

Promotion renames the role in place — all `model_has_roles` assignments are preserved via the foreign key on `role_id`.

::: warning
`clearance-*` permissions are protected outside the install flow — see [`PermissionService`](/en/api/services.md#permissionservice) and the `ClearanceProtectedResourceException` in [Services](/en/api/services.md#exceptions).
:::

## `clearance:backfill`

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

| Flag | Description |
|---|---|
| `--only=meta\|roles\|guards` | Run only one backfill section. |
| `--dry-run` | Preview changes without writing. |

### Sections

| Section | Description |
|---|---|
| `meta` | Seeds a `clr_meta` display name (title-cased role name) for every role without one. |
| `roles` | Seeds `clr_role_meta` defaults (`scope=global`, `is_locked=false`) for every role without one. |
| `guards` | Imports guards from `config('auth.guards')` into `clr_guards`, filtered by `allowed_guard_drivers`. |

All sections are idempotent — running `clearance:backfill` repeatedly is safe and only fills in what's missing.

::: tip
For pre-existing Spatie installs: `clearance:install` never runs Spatie's migrations if the `roles` table already exists, so existing roles, permissions, and assignments are preserved automatically.
:::

## See also

- [Services](/en/api/services.md) — `GuardService` and `MetaService`, which back the `guards` and `meta` backfill sections.
- [Migration guide](/en/guide/migration.md) — migrating an existing Spatie Permission installation to Clearance.
