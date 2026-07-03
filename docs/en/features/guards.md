# Guards

The Guards panel manages authentication guards as database records, so guards can be added or changed without editing `config/auth.php` and redeploying.

## What the panel does

- **Create / edit / delete** guards, each with a `name`, `driver`, and `provider`.

<Screenshot src="/screenshots/guards-page.png" alt="Clearance Guards panel listing database-backed guards" caption="The Guards panel lists every database-backed guard with its driver and provider." />

## How guards are applied

Guards managed from the panel are injected into `auth.guards` at application boot, so they're recognized by Laravel's auth system before any request runs - no manual `config/auth.php` edits needed.

## Allow-listed drivers

Only drivers in the allow-list are accepted:

```php
// config/clearance.php
'allowed_guard_drivers' => ['session', 'token', 'jwt', 'passport', 'sanctum'],
```

::: warning Unlisted drivers are skipped, not rejected loudly
A guard with a driver that isn't in `allowed_guard_drivers` is skipped at boot (with a log warning) rather than corrupting the auth config. If a guard you created isn't taking effect, check the driver against the allow-list first.
:::

## Access control

Read access requires `clearance-access`. Creating, editing, or deleting a guard requires `clearance-guards-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-guards-write');
```

## Route and embedded component

| URI | Route name | Description |
|---|---|---|
| `/clearance/guards` | `clearance.guards` | Guard management (create/edit/delete) |

```blade
<livewire:clearance::guards.guard-manager />
```

## Database

| Table | Key columns |
|---|---|
| `clr_guards` | `name` (unique), `driver`, `provider` |

## Next steps

- [Configuration reference](/en/guide/configuration) - the full `allowed_guard_drivers` and `guards` config keys.
- [Facade reference](/en/api/facade) - `Clearance::guards()` returns the names of all managed authentication guards.
