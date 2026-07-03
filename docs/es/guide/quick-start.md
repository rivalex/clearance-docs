# Inicio Rápido

Tras la [instalación](/es/guide/installation), tienes dos formas de exponer el panel de Clearance: las rutas prediseñadas, o incorporar componentes Livewire individuales en cualquier parte de tu app.

## Opción A - Rutas prediseñadas

Tras la instalación, el panel está disponible en `/clearance` (o el `route_prefix` configurado, consulta [Configuración](/es/guide/configuration)). Todas las rutas están protegidas por el middleware `clearance.access` y usan automáticamente el layout Livewire de la app anfitriona.

| URI | Nombre de ruta | Descripción |
|---|---|---|
| `/clearance` | `clearance.home` | Panel de Control |
| `/clearance/guards` | `clearance.guards` | Gestión de guards (crear/editar/eliminar) |
| `/clearance/permissions` | `clearance.permissions` | CRUD de permisos con validación de nomenclatura por prefijo de grupo |
| `/clearance/roles` | `clearance.roles` | CRUD de roles con asignación de permisos por guard, alcance (global/contextual), y techo |
| `/clearance/settings` | `clearance.settings` | Metadatos de visualización (iconos, colores), rol predeterminado, configuración general |
| `/clearance/user/{userId}` | `clearance.user` | Asignación de roles por usuario + overrides de permisos contextuales (requiere `modules.users = true`) |

Los nombres de ruta permanecen estables sin importar los cambios de prefijo. Para personalizar el middleware:

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

## Opción B - Componentes Livewire incorporados

Cada sección del panel puede montarse individualmente dentro de cualquier vista Blade, página de Filament, o ruta personalizada:

```blade
<livewire:clearance::dashboard />
<livewire:clearance::permissions.permission-manager />
<livewire:clearance::roles.role-manager />
<livewire:clearance::guards.guard-manager />
<livewire:clearance::settings.settings-manager />
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

## Hacia dónde ir ahora

- [Referencia de configuración](/es/guide/configuration) - cada clave de configuración, sus valores predeterminados y su propósito
- [Autorización Contextual](/es/features/contextual-authorization) - limitar roles y permisos a una Tienda, Tenant, o Proyecto
- [Roles](/es/features/roles) - alcance y roles techo
- [Panel de Control](/es/features/dashboard) - qué muestra el componente Panel de Control
- [Usuarios](/es/features/users) - el panel opcional por usuario
