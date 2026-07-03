# Usuarios

El módulo de Usuarios es un panel por usuario para asignar roles - de forma global o limitados a un contexto específico - y para conceder o denegar permisos individuales dentro de un contexto. Es opcional y está desactivado por defecto.

## Activar el módulo

```php
// config/clearance.php
'modules' => ['users' => true],
```

## Qué hace el panel

- **Asignación de rol global** - asigna roles con `scope=global` directamente al usuario, en toda la aplicación.
- **Asignación de rol contextual** - asigna roles con `scope=contextual` limitados a una instancia de modelo específica (una Tienda, Equipo, o Proyecto declarado en `contextual_models`).
- **Overrides de permiso por contexto** - para cada asignación de rol contextual, se pueden conceder o denegar forzosamente permisos adicionales de forma independiente por instancia de contexto.
- **Eliminar asignación** - elimina un rol del usuario (alcance global o contextual), propagando la eliminación de cualquier override asociado.

<Screenshot src="/screenshots/user-panel-global.png" alt="Panel de usuario mostrando la asignación de rol global" caption="Asignando un rol global a un usuario - se aplica en toda la aplicación." />

<Screenshot src="/screenshots/user-panel-contextual.png" alt="Panel de usuario mostrando la asignación de rol contextual y los overrides de permiso por contexto" caption="Asignando un rol contextual y estableciendo overrides de permiso por contexto para una instancia de Tienda específica." />

::: warning Techo de privilegio en la auto-modificación
Tanto la asignación de roles como las concesiones de permisos directas/de override aplican un techo de privilegio: un actor que no sea `super_admin` no puede modificar sus propias asignaciones, ni puede otorgar un permiso que él mismo no posea.
:::

## Control de acceso

El acceso a `/clearance/user/{userId}` requiere `clearance-access` (lectura) y `clearance-users-write` (asignar/eliminar):

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-users-write');
```

## Ruta y componente incorporado

| URI | Nombre de ruta | Descripción |
|---|---|---|
| `/clearance/user/{userId}` | `clearance.user` | Per-user role assignment + contextual permission overrides (requires `modules.users = true`) |

```blade
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

### Enlazar al panel desde tu app

```php
route('clearance.user', ['userId' => $user->id])
```

## Roles contextuales por dentro

Asignar un rol contextual a un usuario crea una fila `UserRoleContext`:

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

Las comprobaciones de autorización respetan el contexto vía `$user->canIn()`, `@canin`, y `ContextService`. Consulta [Autorización Contextual](/es/features/contextual-authorization) para el modelo de resolución completo.

## Base de datos

| Tabla | Columnas clave |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - único por `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## Próximos pasos

- [Autorización Contextual](/es/features/contextual-authorization) - la precedencia de resolución completa para `canIn()`.
- [Roles](/es/features/roles) - configurar el alcance de un rol y sus `context_types`.
- [Referencia de configuración](/es/guide/configuration) - `modules.users`, `contextual_models`.
