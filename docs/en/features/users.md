# Users

The Users module is a per-user panel for assigning roles - globally or scoped to a specific context - and for granting or denying individual permissions within a context. It is opt-in and disabled by default.

## Enabling the module

```php
// config/clearance.php
'modules' => ['users' => true],
```

## What the panel does

- **Global role assignment** - assign `scope=global` roles directly to the user, application-wide.
- **Contextual role assignment** - assign `scope=contextual` roles scoped to a specific model instance (a Store, Team, or Project declared in `contextual_models`).
- **Per-context permission overrides** - for each contextual role assignment, additional permissions can be force-granted or force-denied independently per context instance.
- **Remove assignment** - removes a role from the user (global or contextual scope), cascading the deletion of any associated overrides.

<Screenshot src="/screenshots/user-panel-global.png" alt="User panel showing global role assignment" caption="Assigning a global role to a user - applies application-wide." />

<Screenshot src="/screenshots/user-panel-contextual.png" alt="User panel showing contextual role assignment and per-context permission overrides" caption="Assigning a contextual role and setting per-context permission overrides for a specific Store instance." />

::: warning Privilege ceiling on self-modification
Both role assignment and direct/override permission grants enforce a privilege ceiling: a non-`super_admin` actor cannot modify their own assignments, and cannot grant a permission they don't themselves hold.
:::

## Access control

Access to `/clearance/user/{userId}` requires `clearance-access` (read) and `clearance-users-write` (assign/remove):

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-users-write');
```

## Route and embedded component

| URI | Route name | Description |
|---|---|---|
| `/clearance/user/{userId}` | `clearance.user` | Per-user role assignment + contextual permission overrides (requires `modules.users = true`) |

```blade
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

### Linking to the panel from your app

```php
route('clearance.user', ['userId' => $user->id])
```

## Contextual roles under the hood

Assigning a contextual role to a user creates a `UserRoleContext` row:

```php
use Rivalex\Clearance\Models\UserRoleContext;

// Samuele is "StoreStaff" only within Alessandro's Store
UserRoleContext::create([
    'user_id'      => $samuele->id,
    'role_id'      => $staffRole->id,
    'context_type' => Store::class,
    'context_id'   => $store->id,
]);
```

Authorization checks respect the context via `$user->canIn()`, `@canin`, and `ContextService`. See [Contextual Authorization](/en/features/contextual-authorization) for the full resolution model.

## Database

| Table | Key columns |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - unique per `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## Next steps

- [Contextual Authorization](/en/features/contextual-authorization) - the full resolution precedence for `canIn()`.
- [Roles](/en/features/roles) - configuring a role's scope and `context_types`.
- [Configuration reference](/en/guide/configuration) - `modules.users`, `contextual_models`.
