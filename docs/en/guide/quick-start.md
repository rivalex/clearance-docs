# Quick Start

After [installation](/en/guide/installation), you have two ways to expose the Clearance panel: the pre-built routes, or embedding individual Livewire components anywhere in your app.

## Option A - Pre-built routes

After installation the panel is available at `/clearance` (or the configured `route_prefix`, see [Configuration](/en/guide/configuration)). All routes are protected by the `clearance.access` middleware and use the host app's Livewire layout automatically.

| URI | Route name | Description |
|---|---|---|
| `/clearance` | `clearance.home` | Dashboard |
| `/clearance/guards` | `clearance.guards` | Guard management (create/edit/delete) |
| `/clearance/permissions` | `clearance.permissions` | Permission CRUD with group-prefix naming validation |
| `/clearance/roles` | `clearance.roles` | Role CRUD with guard-scoped permission assignment, scope (global/contextual), and ceiling |
| `/clearance/settings` | `clearance.settings` | Display metadata (icons, colors), default role, general settings |
| `/clearance/user/{userId}` | `clearance.user` | Per-user role assignment + contextual permission overrides (requires `modules.users = true`) |

Route names stay stable regardless of prefix changes. To customize middleware:

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

## Option B - Embedded Livewire components

Each panel section can be mounted individually inside any Blade view, Filament page, or custom route:

```blade
<livewire:clearance::dashboard />
<livewire:clearance::permissions.permission-manager />
<livewire:clearance::roles.role-manager />
<livewire:clearance::guards.guard-manager />
<livewire:clearance::settings.settings-manager />
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

## Where to go next

- [Configuration reference](/en/guide/configuration) - every config key, defaults and purpose
- [Contextual Authorization](/en/features/contextual-authorization) - scope roles and permissions to a Store, Tenant, or Project
- [Roles](/en/features/roles) - scope and ceiling roles
- [Dashboard](/en/features/dashboard) - what the Dashboard component shows
- [Users](/en/features/users) - the opt-in per-user panel
