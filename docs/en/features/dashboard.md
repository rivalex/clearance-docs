# Dashboard

The Dashboard is the landing page of the Clearance panel. It gives an at-a-glance summary of the authorization state of your application - no configuration required.

## What it shows

- **Total roles** - count of all Spatie roles.
- **Total guards** - count of all guards managed by Clearance (see [Guards](/en/features/guards)).
- **Total permissions** - count of all Spatie permissions.
- **Permission groups** - the number of distinct permission groups, derived from the naming prefix (the `group` in `group-action`, e.g. `orders` in `orders-create`). See [Permissions](/en/features/permissions) for the naming convention.
- **Contextual role assignments** - the number of role assignments scoped to a specific model instance (a Store, a Tenant, a Project). See [Contextual Authorization](/en/features/contextual-authorization).
- **Top 5 roles by user count** - the five roles with the most assigned users, ranked.

<Screenshot src="/screenshots/dashboard-page.png" alt="Clearance Dashboard showing role, guard, and permission counts" caption="The Dashboard summarizes roles, guards, permissions, groups, contextual assignments, and top roles." />

## Accessing the Dashboard

| URI | Route name | Description |
|---|---|---|
| `/clearance` | `clearance.home` | Dashboard |

Reading the Dashboard requires the `clearance-access` permission, same as every other panel section. See [Super Admin](/en/features/super-admin#panel-access-fine-grained-write-permissions) for the full access-control table.

## Embedding the component

The Dashboard is a self-contained Livewire component. Mount it anywhere in your own layouts instead of - or in addition to - the pre-built `/clearance` route:

```blade
<livewire:clearance::dashboard />
```

## Next steps

- [Permissions](/en/features/permissions) - manage the permission list feeding the Dashboard's counts.
- [Roles](/en/features/roles) - manage the roles behind the "top 5 by user count" stat.
- [Contextual Authorization](/en/features/contextual-authorization) - what a contextual role assignment is.
