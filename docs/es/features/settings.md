# Configuración

El panel de Configuración establece cómo se muestran los roles y guards en el resto del panel de Clearance, además del rol predeterminado asignado a los nuevos usuarios.

## Qué hace el panel

- **Metadatos de visualización** por rol/guard - nombre de visualización, descripción, y un icono SVG sanitizado + color hexadecimal, mostrados en todo el panel.
- **Rol predeterminado** - el rol asignado automáticamente a los usuarios recién registrados cuando `auto_assign_default_role` es `true`.
- **Asignación masiva del rol predeterminado** - asignación con un clic del rol predeterminado actual a todos los usuarios existentes que aún no lo tengan.
- **Mostrar iconos** - activa/desactiva la visualización de iconos en todo el panel.

<Screenshot src="/screenshots/settings-page.png" alt="Panel de Configuración de Clearance con rol predeterminado y el interruptor de mostrar iconos" caption="El panel de Configuración controla el rol predeterminado, la asignación masiva, y la visualización de iconos." />

## Metadatos de visualización por rol/guard

<Screenshot src="/screenshots/settings-role-meta.png" alt="Formulario de metadatos de visualización de rol con selectores de icono y color" caption="Editando el nombre de visualización, descripción, icono y color de un rol." />

Cada icono SVG proporcionado por el usuario pasa por un sanitizador basado en lista de permitidos antes de almacenarse y de nuevo al renderizarse, de modo que los metadatos de visualización pueden editarse sin introducir marcado inseguro en el panel.

## Rol predeterminado y auto-asignación

```php
// config/clearance.php
'auto_assign_default_role' => true, // requires a default role set in Settings
```

::: tip Establece el rol predeterminado antes de activar la auto-asignación
`auto_assign_default_role` no tiene efecto hasta que se configura un rol predeterminado en el panel de Configuración. Establece primero el rol predeterminado, y luego activa el flag.
:::

Usa **Asignación masiva** en el panel en cualquier momento para asignar retroactivamente el rol predeterminado actual a los usuarios existentes que aún no lo tengan - útil tras cambiar el rol predeterminado, o tras activar `auto_assign_default_role` en una aplicación con usuarios existentes.

## Control de acceso

El acceso de lectura requiere `clearance-access`. Editar metadatos de visualización, iconos, colores, o el rol predeterminado requiere `clearance-settings-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-settings-write');
```

## Ruta y componente incorporado

| URI | Nombre de ruta | Descripción |
|---|---|---|
| `/clearance/settings` | `clearance.settings` | Display metadata (icons, colors), default role, general settings |

```blade
<livewire:clearance::settings.settings-manager />
```

## Base de datos

| Tabla | Columnas clave |
|---|---|
| `clr_meta` | `subject_type`, `subject_key` (únicos en conjunto) - `display_name`, `description`, `icon_svg`, `color` |
| `clr_settings` | `key` (único), `value` |

## Próximos pasos

- [Roles](/es/features/roles) - los roles cuyos metadatos de visualización se configuran aquí.
- [Guards](/es/features/guards) - los guards cuyos metadatos de visualización se configuran aquí.
- [Seguridad](/es/guide/security) - cómo funciona el sanitizador de SVG.
