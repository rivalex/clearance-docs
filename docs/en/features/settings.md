# Settings

The Settings panel configures how roles and guards are displayed throughout the rest of the Clearance panel, plus the default role assigned to new users.

## What the panel does

- **Display metadata** per role/guard - display name, description, and a sanitized SVG icon + hex color, shown throughout the panel.
- **Default role** - the role automatically assigned to newly registered users when `auto_assign_default_role` is `true`.
- **Bulk-assign default role** - one-click assignment of the current default role to every existing user who doesn't already have it.
- **Show icons** - toggles icon display across the panel.

<Screenshot src="/screenshots/settings-page.png" alt="Clearance Settings panel with default role and show-icons toggle" caption="The Settings panel controls the default role, bulk-assign, and icon display." />

## Display metadata per role/guard

<Screenshot src="/screenshots/settings-role-meta.png" alt="Role display metadata form with icon and color pickers" caption="Editing display name, description, icon, and color for a role." />

Every user-supplied SVG icon passes through an allow-list sanitizer before storage and again at render time, so display metadata can be edited without introducing unsafe markup into the panel.

## Default role and auto-assign

```php
// config/clearance.php
'auto_assign_default_role' => true, // requires a default role set in Settings
```

::: tip Set the default role before enabling auto-assign
`auto_assign_default_role` has no effect until a default role is configured in the Settings panel. Set the default role first, then enable the flag.
:::

Use **Bulk-assign** in the panel at any time to retroactively assign the current default role to existing users who don't already have it - useful after changing the default role, or after enabling `auto_assign_default_role` on an application with existing users.

## Access control

Read access requires `clearance-access`. Editing display metadata, icons, colors, or the default role requires `clearance-settings-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-settings-write');
```

## Route and embedded component

| URI | Route name | Description |
|---|---|---|
| `/clearance/settings` | `clearance.settings` | Display metadata (icons, colors), default role, general settings |

```blade
<livewire:clearance::settings.settings-manager />
```

## Database

| Table | Key columns |
|---|---|
| `clr_meta` | `subject_type`, `subject_key` (unique together) - `display_name`, `description`, `icon_svg`, `color` |
| `clr_settings` | `key` (unique), `value` |

## Next steps

- [Roles](/en/features/roles) - the roles whose display metadata is configured here.
- [Guards](/en/features/guards) - the guards whose display metadata is configured here.
- [Security](/en/guide/security) - how the SVG sanitizer works.
