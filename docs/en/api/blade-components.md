# Blade Components

Clearance registers a small set of Blade components under the `clearance::` namespace. All are reusable directly in your own application views, not just inside the panel.

## Components

### `x-clearance::message`

Flash/status message display.

```blade
<x-clearance::message>
    Changes saved successfully.
</x-clearance::message>
```

### `x-clearance::clipboard`

Copy-to-clipboard button/wrapper — used, for example, to show route names or IDs with a one-click copy action.

```blade
<x-clearance::clipboard :value="$route->name" />
```

### `x-clearance::validation-errors`

Standard Laravel validation error list renderer.

```blade
<x-clearance::validation-errors />
```

### `x-clearance::icon.*`

A small bundled icon set, used throughout the panel. No external JS dependency beyond Alpine, which Livewire already requires.

```blade
<x-clearance::icon.check />
```

## Livewire components

For reference, these are the full-page/embeddable Livewire component names registered by the package, usable with `<livewire:clearance::...>` embedding in your own views:

| Component name | Description |
|---|---|
| `dashboard` | The panel dashboard. |
| `permissions.permission-manager` | Permissions CRUD panel. |
| `roles.role-manager` | Roles CRUD panel, including ceiling roles. |
| `guards.guard-manager` | Guards CRUD panel. |
| `settings.settings-manager` | Settings panel (display metadata, icons, colors). |
| `users.user-clearance-manager` | Per-user role/permission assignment panel. Takes `:userId`. |

```blade
<livewire:clearance::dashboard />

<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

See the corresponding feature pages for each panel: [Dashboard](/en/features/dashboard.md), [Permissions](/en/features/permissions.md), [Roles](/en/features/roles.md), [Guards](/en/features/guards.md), [Settings](/en/features/settings.md), [Users](/en/features/users.md).

::: tip
The `config('clearance.ui.flux_pro')` key controls whether Pro-only Flux UI elements render in these components. `null` (the default) auto-detects Flux Pro via `Flux::pro()`.
:::
