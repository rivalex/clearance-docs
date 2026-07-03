# Architecture

## How resolution works

`resolveFor()` is the core of Clearance's contextual authorization - see [Contextual Authorization](/en/features/contextual-authorization) for the full API. It merges three sources, in this precedence order:

1. **Contextual role grants** - permissions from roles the user holds specifically within this context (`clr_role_ctx`).
2. **Global role grants** - permissions from the user's globally-assigned Spatie roles (so `super_admin` and other global roles satisfy `canIn()` for any context).
3. **Per-context overrides** (`clr_ctx_overrides`) - `forced_on` adds a permission on top of the above; `forced_off` removes it, even if the permission was granted by a global role. A deny always wins.

::: warning A deny always wins
`forced_off` overrides any permission granted by a global role or a contextual role grant. There is no way to "out-rank" a `forced_off` override for the same user/permission/context short of removing it.
:::

## Database

Clearance owns 6 tables and never alters Spatie's core tables (`roles`, `permissions`, `model_has_roles`, `model_has_permissions`, `role_has_permissions`). All FKs referencing `roles.id`/`permissions.id` cascade on delete unless noted; Spatie tables have no FKs pointing back to `clr_*`.

| Table | Key columns |
|---|---|
| `clr_meta` | `subject_type`, `subject_key` (unique together) - `display_name`, `description`, `icon_svg`, `color` - display metadata for a role or guard |
| `clr_role_meta` | `role_id` (unique, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (nullable FK -> `roles`, `ON DELETE SET NULL`) - ceiling role |
| `clr_guards` | `name` (unique), `driver`, `provider` - guards managed via the panel |
| `clr_settings` | `key` (unique), `value` - runtime key/value store (default role, etc.) |
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - contextual role assignments, unique per `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) - per-user, per-context permission overrides |

## Never touches Spatie's tables

Clearance is designed to sit alongside `spatie/laravel-permission`, not replace it. Every `Role` and `Permission` record a Clearance panel creates or edits is a genuine Spatie model, stored in Spatie's own tables. Clearance never runs a migration that adds a column to, or writes an FK from, any of `roles`, `permissions`, `model_has_roles`, `model_has_permissions`, or `role_has_permissions`.

All of Clearance's own data - display metadata, role scope and ceilings, contextual assignments, per-context overrides, guard definitions, and settings - lives exclusively in the 6 `clr_*` tables above. This means:

- Uninstalling Clearance never risks corrupting your existing Spatie role/permission data.
- Any code that already reads Spatie's models or tables directly keeps working unmodified.
- Clearance's contextual and ceiling features are purely additive layers resolved at query time (via `resolveFor()` and `RoleService`), not baked into Spatie's schema.

## Related

- [Contextual Authorization](/en/features/contextual-authorization) - `canIn()`, `hasRoleIn()`, Blade directives, `ContextService`
- [Roles](/en/features/roles) - scope and ceiling roles, `RoleService::setParent()`
- [Migration](/en/guide/migration) - backfilling `clr_meta`/`clr_role_meta`/`clr_guards` onto an existing Spatie install
- [Security](/en/guide/security) - how the ceiling and override model prevents privilege escalation
