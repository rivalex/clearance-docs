# Migrating from an existing Spatie install

If your application already uses `spatie/laravel-permission` with existing roles, permissions, and assignments, `clearance:install` detects and preserves them - it never runs Spatie's migrations if the `roles` table already exists. See [Installation](/en/guide/installation) for the full install flow.

Once installed, use `clearance:backfill` to adopt Clearance's own metadata onto your existing roles and guards.

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

## What each section does

| Section | Effect |
|---|---|
| `meta` | Seeds a `clr_meta` display name (title-cased role name) for every role without one. |
| `roles` | Seeds `clr_role_meta` defaults (`scope=global`, `is_locked=false`) for every role without one. |
| `guards` | Imports guards from `config('auth.guards')` into `clr_guards`, filtered by `allowed_guard_drivers`. |

Use `--only=<section>` to run a single section instead of all three.

::: tip Idempotent
All sections are idempotent - re-running `clearance:backfill` is safe and won't duplicate or overwrite existing `clr_*` rows.
:::

## Edge cases

The package repository has a deeper migration guide covering edge cases not summarized here - notably what happens when your database already has a role named `super_admin` (or an alias like `superadmin`, `root`, `owner`) that collides with Clearance's own `super_admin` role, and notes on custom `Role`/`Permission` model subclasses and guard import behavior. Read it before backfilling a production database with non-trivial existing role data. See also [Super Admin](/en/features/super-admin) for alias detection behavior during `clearance:install`.

## Related

- [Installation](/en/guide/installation) - the full `clearance:install` flow
- [Artisan commands](/en/api/artisan-commands) - full command reference
- [Architecture](/en/guide/architecture) - the `clr_*` tables that backfill populates
