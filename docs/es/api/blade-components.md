# Componentes Blade

Clearance registra un pequeño conjunto de componentes Blade bajo el namespace `clearance::`. Todos son reutilizables directamente en las vistas de tu propia aplicación, no solo dentro del panel.

## Componentes

### `x-clearance::message`

Visualización de mensajes flash/de estado.

```blade
<x-clearance::message>
    Changes saved successfully.
</x-clearance::message>
```

### `x-clearance::clipboard`

Botón/envoltorio de copiar al portapapeles — usado, por ejemplo, para mostrar nombres de ruta o IDs con una acción de copia de un clic.

```blade
<x-clearance::clipboard :value="$route->name" />
```

### `x-clearance::validation-errors`

Renderizador de lista de errores de validación estándar de Laravel.

```blade
<x-clearance::validation-errors />
```

### `x-clearance::icon.*`

Un pequeño conjunto de iconos incluido, usado en todo el panel. Sin dependencia externa de JS más allá de Alpine, que Livewire ya requiere.

```blade
<x-clearance::icon.check />
```

## Componentes Livewire

Como referencia, estos son los nombres completos de los componentes Livewire de página completa/incorporables registrados por el paquete, utilizables con la incorporación `<livewire:clearance::...>` en tus propias vistas:

| Nombre del componente | Descripción |
|---|---|
| `dashboard` | El panel de control del panel. |
| `permissions.permission-manager` | Panel CRUD de Permisos. |
| `roles.role-manager` | Panel CRUD de Roles, incluyendo roles techo. |
| `guards.guard-manager` | Panel CRUD de Guards. |
| `settings.settings-manager` | Panel de Configuración (metadatos de visualización, iconos, colores). |
| `users.user-clearance-manager` | Panel de asignación de roles/permisos por usuario. Recibe `:userId`. |

```blade
<livewire:clearance::dashboard />

<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

Consulta las páginas de funcionalidades correspondientes a cada panel: [Panel de Control](/es/features/dashboard.md), [Permisos](/es/features/permissions.md), [Roles](/es/features/roles.md), [Guards](/es/features/guards.md), [Configuración](/es/features/settings.md), [Usuarios](/es/features/users.md).

::: tip
La clave `config('clearance.ui.flux_pro')` controla si los elementos de Flux UI exclusivos de Pro se renderizan en estos componentes. `null` (el valor predeterminado) auto-detecta Flux Pro vía `Flux::pro()`.
:::
