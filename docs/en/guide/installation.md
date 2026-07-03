# Installation

## Requirements

See the full [requirements table](/en/guide/introduction#requirements) on the Introduction page - PHP ^8.3, Laravel ^11.0|^12.0|^13.0, spatie/laravel-permission ^6.0, livewire/livewire ^3.0|^4.0, livewire/flux ^2.14.

## Install the package

```bash
composer require rivalex/clearance
```

## Run the installer

```bash
php artisan clearance:install
```

::: danger Always run clearance:install, never a plain migrate
Clearance's own migrations depend on Spatie's `roles`/`permissions` tables existing first; the installer publishes and runs Spatie's migrations before its own. If you run `php artisan migrate` directly on a fresh setup and Spatie isn't set up yet, you'll get an actionable error message telling you to run the installer instead.
:::

### Optional flags

| Flag | Description |
|---|---|
| `--user=ID` | Assign the `super_admin` role to a user after install |
| `--role=NAME` | Assign `clearance-access` to a role (created if absent) |
| `--super-admin-role=NAME` | Promote an existing role to `super_admin` (skips interactive prompt) |
| `--force` | Re-run even if already installed |

### What the installer does

1. Publishes `config/clearance.php`.
2. Publishes Clearance's migrations.
3. Detects Spatie Permission tables - if the `roles` table is absent, publishes and runs Spatie's migrations automatically first.
4. Runs all pending migrations.
5. Creates `clearance-access` and all 5 fine-grained capability permissions: `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`.
6. Creates the `super_admin` role with all `clearance-*` permissions assigned (additive - never removes existing permissions on re-run).
7. Writes `storage/.clearance-installed` as an idempotency marker.

::: tip Migrating from an existing Spatie install?
Use `php artisan clearance:backfill` instead of starting from scratch, and read the [Migration guide](/en/guide/migration) first - it covers `super_admin` collision, custom model subclasses, and guard import.
:::

## Add HasClearance to your User model

To use contextual authorization methods (`$user->canIn()`, `$user->hasRoleIn()`), add the `HasClearance` trait to `App\Models\User`. It already includes Spatie's `HasRoles` - one line replaces both:

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance; // includes HasRoles + all contextual authorization methods
}
```

::: tip Already using `use HasRoles`?
Replace it with `use HasClearance`, or keep both - PHP deduplicates trait composition automatically, no conflicts.
:::

Without this trait, `$user->canIn()` and related methods are unavailable, but the Blade directives (`@canin`, `@hasrolein`) and `ContextService` keep working regardless - they don't depend on the trait. See [Contextual Authorization](/en/features/contextual-authorization) for details.

## Next steps

Continue to the [Quick Start](/en/guide/quick-start) to mount the panel and start managing permissions.
