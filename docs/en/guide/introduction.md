# Introduction

## What is Clearance?

Clearance is a drop-in admin panel for Laravel applications built on top of [spatie/laravel-permission](https://github.com/spatie/laravel-permission). It adds a Livewire 4 + Flux UI panel for managing permissions, roles, guards and settings, plus a contextual authorization layer and role ceilings - installable with one artisan command, without altering any Spatie table.

Clearance gives you:

- **Admin panel** for Permissions, Roles, Guards, Settings and a Dashboard - use the pre-built routes or embed each Livewire component individually.
- **Contextual authorization** - grant a role or permission scoped to a specific model instance (a Store, a Tenant, a Project), with `$user->canIn()`, `@canin`/`@hasrolein` Blade directives, and a `ContextService` for use outside the User model.
- **Ceiling roles** - a role can declare a parent role whose permission set acts as an upper bound; a child role can never exceed its parent, enforced automatically on every save.
- **Fine-grained write permissions** - one delegable `clearance-{section}-write` permission per panel section, so you can hand out "manage roles" without also handing out "manage settings."
- **Super Admin** with an opt-in global Gate bypass, alias detection for existing admin-like roles, and safe non-destructive promotion.
- **Users module** (opt-in) - assign roles globally or to a specific context, plus per-context permission overrides, from a per-user panel.
- **Settings panel** - per-role/guard display name, description, sanitized SVG icon and color; a configurable default role with auto-assign and bulk-assign.
- **Guards management** - database-backed guards, injected into `auth.guards` at boot, restricted to an allow-listed set of drivers.
- **9 bundled languages** (ar, en, es, fr, hi, it, pt, ru, zh), key-parity tested.
- **Extensibility hooks** - `HasClearance` trait, `HasPermissionGroups` trait, `ClearanceContextable` contract, reusable Blade components.
- **SVG sanitization** - every user-supplied icon passes through an allow-list sanitizer before storage and at render time.
- **Security-hardened** - privilege-escalation ceilings on every permission-granting path, deny-authoritative context overrides, protected `clearance-*` namespace. See [Security](/en/guide/security).

## Relationship to spatie/laravel-permission

Clearance is not a replacement for Spatie's permission package - it's a UI and authorization layer on top of it. Roles and permissions are still Spatie's `Role` and `Permission` models, still stored in Spatie's `roles`, `permissions`, `model_has_roles`, `model_has_permissions` and `role_has_permissions` tables. Clearance never alters those tables. Everything Clearance adds - display metadata, role scope, ceilings, contextual assignments, per-context overrides - lives in its own set of `clr_*` tables. See [Architecture](/en/guide/architecture) for the full breakdown.

## Requirements

| Dependency | Version |
|---|---|
| PHP | ^8.3 |
| Laravel | ^11.0 \| ^12.0 \| ^13.0 |
| spatie/laravel-permission | ^6.0 |
| livewire/livewire | ^3.0 \| ^4.0 |
| livewire/flux | ^2.14 |

## Key concepts

| Concept | Summary |
|---|---|
| Permission | A Spatie permission following the `group-action` naming convention (e.g. `orders-create`). Grants an ability. |
| Role scope | Every role is `global` (assigned directly, applies everywhere) or `contextual` (bound to specific model types, assignable only within a context instance). |
| Ceiling role | A role can declare a parent role whose permission set is an upper bound - the child can never exceed it. |
| Context | A model instance (a Store, a Tenant, a Project) that a role or permission override can be scoped to. |
| Override | A per-user, per-context `forced_on`/`forced_off` permission grant or denial - a deny always wins. |

Each of these gets a full page: [Permissions](/en/features/permissions), [Roles](/en/features/roles), [Contextual Authorization](/en/features/contextual-authorization), [Users](/en/features/users), [Super Admin](/en/features/super-admin).

## Next steps

Ready to install Clearance? Continue to the [Installation guide](/en/guide/installation).
