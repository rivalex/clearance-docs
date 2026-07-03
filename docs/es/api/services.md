# Servicios

Todos los servicios de Clearance viven en `src/Services/` y se resuelven desde el contenedor, p. ej. `app(ContextService::class)`. Son la capa a la que recurrir desde controladores, políticas, y jobs — en cualquier lugar donde el modelo `User` pueda no usar el trait `HasClearance`.

## `ContextService`

`Rivalex\Clearance\Services\ContextService` (`src/Services/ContextService.php`) es el motor detrás de la autorización contextual. El [facade `Clearance`](/es/api/facade.md) y el trait `HasClearance` delegan ambos en él.

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

`resolveFor()` construye su resultado usando una precedencia de 3 fuentes:

1. Concesiones de rol contextual
2. Concesiones de rol global
3. Overrides por contexto (`forced_on` añade, `forced_off` siempre gana)

Consulta [Autorización Contextual](/es/features/contextual-authorization.md) para el recorrido conceptual de esta precedencia.

## `RoleService`

`Rivalex\Clearance\Services\RoleService` (`src/Services/RoleService.php`) gestiona los roles techo.

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
app(RoleService::class)->removeParent($childRole);
```

| Método | Descripción |
|---|---|
| `setParent($childRole, $parentRole)` | Establece un techo: los permisos excedentes en `$childRole` más allá del conjunto del padre se recortan silenciosamente. Lanza `ClearanceScopeViolationException` si el hijo ya es un padre, o el padre ya es un hijo. |
| `removeParent($childRole)` | Elimina la relación de techo. |

::: warning
Un techo solo recorta — nunca concede. Un rol padre permisivo no otorga, por sí mismo, ningún permiso al hijo. Eliminar un permiso de un rol padre propaga esa eliminación a cada rol hijo automáticamente.
:::

Consulta [Roles](/es/features/roles.md) para el concepto de rol techo en más profundidad.

## `PermissionService`

`Rivalex\Clearance\Services\PermissionService` (`src/Services/PermissionService.php`) respalda el CRUD del panel de Permisos.

Aplica la convención de nomenclatura `group-action`, controlada por las claves de configuración `enforce_naming_convention` y `naming_separator`, y protege el namespace de permisos `clearance-*` frente a ser creado, renombrado, o eliminado fuera del flujo de instalación.

## `GuardService`

`Rivalex\Clearance\Services\GuardService` (`src/Services/GuardService.php`) respalda el CRUD del panel de Guards.

Inyecta guards respaldados por BD en `auth.guards` al arrancar, filtrados por la clave de configuración `allowed_guard_drivers` — un driver no listado se omite con una advertencia en el log en lugar de corromper la configuración de auth.

## `MetaService`

`Rivalex\Clearance\Services\MetaService` (`src/Services/MetaService.php`) lee y escribe `clr_meta` (nombre de visualización, descripción, icono SVG sanitizado, color) para un sujeto de rol o guard. Respalda el editor de metadatos de visualización del panel de Configuración.

Los iconos SVG pasan por `Rivalex\Clearance\Support\SvgSanitizer` (un sanitizador basado en lista de permitidos) tanto antes de almacenarse como al renderizarse.

## `UserClearanceService`

`Rivalex\Clearance\Services\UserClearanceService` (`src/Services/UserClearanceService.php`) respalda el panel del módulo de Usuarios:

- Asignación de rol global
- Asignación de rol contextual (vía `UserRoleContext`)
- Overrides de permiso por contexto (vía `UserContextPermissionOverride`, `forced_on` / `forced_off`)
- Eliminación de asignaciones, que propaga la eliminación de overrides

Aplica un techo de privilegio: un actor que no sea `super_admin` no puede modificar sus propias asignaciones, ni puede otorgar un permiso que él mismo no posea. Consulta [Super Admin](/es/features/super-admin.md) y [Usuarios](/es/features/users.md).

## Excepciones

Todas en `src/Exceptions/`:

| Excepción | Se lanza cuando |
|---|---|
| `ClearanceConfigException` | Se lee un valor inválido o mal configurado de `config/clearance.php` (p. ej. un `naming_separator` incorrecto). |
| `ClearanceNamingException` | Un nombre de permiso viola la convención de nomenclatura `group-action` mientras la aplicación de la regla está activada. |
| `ClearanceProtectedResourceException` | Se intenta modificar un permiso `clearance-*` protegido, o un recurso bloqueado, fuera del flujo de instalación. |
| `ClearanceScopeViolationException` | Una relación de rol techo inválida (un padre intentando convertirse en hijo, o viceversa), o un desajuste de alcance. |

## Ver también

- [Facade](/es/api/facade.md) — el envoltorio de llamada estática alrededor de `ContextService`.
- [Traits y Directivas](/es/api/traits-directives.md) — los equivalentes de método de instancia y directiva Blade.
- [Comandos Artisan](/es/api/artisan-commands.md) — `clearance:install` y `clearance:backfill`, que siembran los datos sobre los que operan estos servicios.
