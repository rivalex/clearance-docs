# Roles

El panel de Roles es una interfaz CRUD completa sobre el modelo `Role` de Spatie, extendida con dos conceptos específicos de Clearance: **alcance** (global vs. contextual) y **techo** (un rol padre que limita los permisos de un hijo).

## Qué hace el panel

- **Crear / editar / eliminar** roles.
- **Asignación de permisos por guard** - asigna permisos a un rol, filtrados por el guard del rol.
- Selección de **alcance** - `global` o `contextual`.
- Configuración de **techo** - establecer o eliminar un rol padre.

<Screenshot src="/screenshots/roles-page.png" alt="Panel de Roles de Clearance listando roles con alcance y techo" caption="El panel de Roles lista cada rol con su alcance y, si está configurado, su rol techo padre." />

## Alcance: global vs. contextual

Todo rol declara un alcance en el momento de su creación:

- **`global`** - asignado directamente a un usuario vía `$user->assignRole()`, se aplica en todas partes.
- **`contextual`** - vinculado a uno o más tipos de modelo (`context_types`), asignable solo dentro de una instancia de contexto específica vía `UserRoleContext`. Consulta [Autorización Contextual](/es/features/contextual-authorization) y [Usuarios](/es/features/users#contextual-roles-under-the-hood).

Los roles sin una fila `RoleMeta` explícita usan `global` por defecto, por compatibilidad hacia atrás.

## Roles techo

Un rol puede declarar un techo - un rol padre cuyo conjunto de permisos actúa como límite superior:

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
```

<Screenshot src="/screenshots/role-form-ceiling.png" alt="Formulario de rol con un rol techo padre seleccionado" caption="Estableciendo un rol techo padre en el formulario de Rol (RoleForm)." />

Reglas aplicadas automáticamente en cada guardado:

- El hijo solo puede poseer permisos que el padre también posea. Cualquier exceso se recorta silenciosamente en `setParent()` y en cada sincronización de permisos posterior - sin excepción, sin mensaje de error, simplemente se aplica silenciosamente.
- Un rol que ya actúa como padre no puede convertirse en hijo (`ClearanceScopeViolationException`).
- Un rol que ya es hijo no puede a su vez actuar como padre (`ClearanceScopeViolationException`).
- Eliminar permisos de un padre propaga la eliminación a cada hijo automáticamente.

::: warning Un techo solo recorta, nunca concede
Establecer un padre permisivo no otorga nada al hijo por sí mismo. El techo es estrictamente un límite superior - nunca añade permisos al hijo, solo puede eliminarlos.
:::

Esto se configura por rol en el panel de Roles (`RoleForm`), o programáticamente vía `RoleService::setParent()` / `RoleService::removeParent()`.

## Control de acceso

El acceso de lectura requiere `clearance-access`. Crear, editar o eliminar un rol - incluyendo sus asignaciones de permisos, alcance y techo - requiere `clearance-roles-write`:

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

## Ruta y componente incorporado

| URI | Nombre de ruta | Descripción |
|---|---|---|
| `/clearance/roles` | `clearance.roles` | Role CRUD with guard-scoped permission assignment, scope (global/contextual), and ceiling |

```blade
<livewire:clearance::roles.role-manager />
```

## Base de datos

Los metadatos de rol (alcance, techo, estado de bloqueo) viven en `clr_role_meta`, nunca en la tabla `roles` de Spatie:

| Tabla | Columnas clave |
|---|---|
| `clr_role_meta` | `role_id` (único, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (FK anulable -> `roles`, `ON DELETE SET NULL`) |

## Próximos pasos

- [Autorización Contextual](/es/features/contextual-authorization) - cómo se verifican en tiempo de ejecución los roles con `scope=contextual`.
- [Usuarios](/es/features/users) - asignar roles a un usuario específico, de forma global o por contexto.
- [Referencia de servicios](/es/api/services) - la API completa de `RoleService`.
