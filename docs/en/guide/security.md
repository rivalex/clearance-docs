# Security

Clearance went through a dedicated pre-publication security audit plus two follow-up hardening passes before release. Highlights: privilege-escalation ceilings on every permission-granting path (direct grants and role-level syncing alike), deny-authoritative context overrides, an allow-list SVG sanitizer, a protected `clearance-*` permission namespace, and guest-safe Blade directives.

## Panel access & fine-grained write permissions

Read access to the whole panel requires the `clearance-access` permission (checked via `can()`, never `hasRole()`). Write operations on each section are gated by a dedicated, delegable permission - all created by `clearance:install` and assigned to `super_admin`:

| Permission | Controls |
|---|---|
| `clearance-permissions-write` | Create / edit / delete permissions |
| `clearance-roles-write` | Create / edit / delete roles and their permission assignments |
| `clearance-guards-write` | Create / edit / delete guards |
| `clearance-settings-write` | Edit display metadata, icons, colors, default role |
| `clearance-users-write` | Assign / remove roles for a specific user |

If a fine-grained permission row doesn't exist at all in the database (a legacy pre-seeding state), the check falls back to `clearance-access` for backwards compatibility.

## Protected `clearance-*` namespace

`clearance-*` permissions are reserved: they cannot be created, renamed, deleted, or added to any role except through the package's own install flow - even by a `super_admin` actor for the `super_admin` role itself.

## Privilege-escalation ceilings

Every permission-granting path in Clearance enforces a ceiling so an actor can never grant more than they themselves hold:

- **Ceiling roles** - a role can declare a parent role whose permission set acts as an upper bound. Any excess permission is silently trimmed on `RoleService::setParent()` and on every subsequent permission sync - no exception, no error message. Removing permissions from a parent cascades the removal to every child automatically. See [Roles](/en/features/roles) for the full ceiling API.
- **Users module** - both role assignment and direct/override permission grants enforce a privilege ceiling: a non-`super_admin` actor cannot modify their own assignments, and cannot grant a permission they don't themselves hold. See [Users](/en/features/users).

## Deny-authoritative context overrides

Per-context permission overrides (`clr_ctx_overrides`) support `forced_on` and `forced_off`. `forced_off` always wins - even over a permission granted by a global role. There is no path that lets a lower-precedence grant override an explicit deny. See [Architecture](/en/guide/architecture#how-resolution-works) for the full resolution order.

## SVG sanitization

Every user-supplied icon (role/guard display metadata in the Settings panel) passes through an allow-list sanitizer both before storage and at render time - not a one-time check at upload.

## Guest-safe Blade directives

`@canin` and `@hasrolein` both resolve safely (return `false`) for a guest user - they never throw.

## Gate bypass is application-wide

`super_admin_gate_bypass` (default `false`) controls whether the `super_admin` role bypasses all Laravel `can()` / `Gate::allows()` / policy checks globally, via a `Gate::before()` hook.

::: warning Not scoped to the panel
When enabled, any user holding `super_admin` skips all Gate checks application-wide - not only Clearance panel checks. Enable it deliberately. See [Super Admin](/en/features/super-admin) for alias detection and promotion behavior.
:::

## Security-relevant gotchas

- `forced_off` always wins - even over a permission granted by a global role.
- A ceiling role only trims, never grants - setting a permissive parent grants the child nothing by itself.
- The Gate bypass (`super_admin_gate_bypass`) is application-wide, not panel-scoped.
- The Users module is off by default (`modules.users = false`).
- `clearance-*` permissions are untouchable outside the install flow, even for `super_admin`.
- Always run `clearance:install`, not a plain `migrate`, on a fresh setup - see [Installation](/en/guide/installation).

## Related

- [Architecture](/en/guide/architecture) - resolution precedence and the `clr_*` schema
- [Roles](/en/features/roles) - ceiling roles in depth
- [Users](/en/features/users) - the privilege ceiling on role/permission assignment
- [Super Admin](/en/features/super-admin) - Gate bypass and alias detection
