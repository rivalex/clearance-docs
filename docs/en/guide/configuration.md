# Configuration

Clearance ships with sensible defaults, so it works out of the box after [installation](/en/guide/installation). Publish the config file when you need to customize the route prefix, middleware, contextual models, or any other setting.

```bash
php artisan vendor:publish --tag=clearance-config
```

This publishes `config/clearance.php`.

## Configuration reference

This is the definitive reference for every key in `config/clearance.php`. Other pages in these docs link back here instead of repeating the table.

| Key | Default | Purpose |
|---|---|---|
| `route_prefix` | `'clearance'` | URI prefix for all panel routes. |
| `middleware` | `['web', 'auth']` | Applied to all panel routes before `clearance.access`. |
| `access_permission` | `'clearance-access'` | Permission checked by the access middleware. |
| `user_model` | `null` | Defaults to `auth.providers.users.model`. |
| `modules.users` | `false` | Master switch for the Users module (routes + Livewire components). |
| `contextual_models` | `[]` | `FQCN => ['label', 'icon', 'label_attribute', 'searchable']` - models that can act as role-binding contexts. |
| `enforce_naming_convention` | `true` | Enforce `group-action` permission naming. |
| `naming_separator` | `'-'` | Separator character - `'-'` or `'_'` only. |
| `guards` | `[]` | Override auto-detected guards from `config/auth.php`. Empty = auto-detect all. |
| `allowed_guard_drivers` | `['session', 'token', 'jwt', 'passport', 'sanctum']` | Drivers accepted when injecting DB guards into `auth.guards`. |
| `layout` | `null` | Blade layout for full-page components. `null` = host app's default (`config('livewire.layout')`). |
| `auto_assign_default_role` | `false` | Auto-assign the Settings-configured default role on `Registered` event. |
| `super_admin_gate_bypass` | `false` | Opt-in: `super_admin` bypasses all Gate checks application-wide. |
| `ui.flux_pro` | `null` | `null` = auto-detect via `Flux::pro()`. |

## Permission naming convention

Permissions must follow the `group-action` format:

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

Disable enforcement per environment with `enforce_naming_convention`:

```php
'enforce_naming_convention' => false,
```

::: warning Separator is validated at boot
The `naming_separator` value is the only piece of this config that reaches a raw SQL fragment (grouping/sorting permissions), so only `-` or `_` are accepted.
:::

## Customizing middleware

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

Route names stay stable regardless of prefix or middleware changes - see the [Quick Start](/en/guide/quick-start) for the full routes table.

## Related

- [Quick Start](/en/guide/quick-start) - mounting the panel with these settings applied
- [Publishing assets](/en/guide/publishing) - publishing translations and understanding the `layout` key
- [Guards](/en/features/guards) - how `guards` and `allowed_guard_drivers` are used
- [Super Admin](/en/features/super-admin) - how `super_admin_gate_bypass` works
