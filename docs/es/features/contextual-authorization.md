# Autorización Contextual

Clearance extiende el modelo de autorización global de Spatie con comprobaciones limitadas a un contexto. Los roles de un usuario pueden asignarse dentro de un contexto específico (p. ej. una Tienda, Tenant, o Proyecto) y son invisibles fuera de ese alcance. Este es el mecanismo central detrás del [módulo de Usuarios](/es/features/users) y de los [Roles](/es/features/roles#scope-global-vs-contextual) contextuales.

## En el modelo User

Disponible cuando tu modelo `User` usa el trait `HasClearance` (consulta [Instalación](/es/guide/installation#add-hasclearance-to-your-user-model)):

```php
// Does the user have this permission in this context?
$user->canIn('orders-create', $store);

// Alias - mirrors Spatie's hasPermissionTo() naming
$user->hasPermissionIn('orders-create', $store);

// Does the user hold this role in this context?
$user->hasRoleIn('manager', $store);

// Optionally filter by guard
$user->canIn('orders-create', $store, 'api');
$user->hasRoleIn('manager', $store, 'web');
```

## En vistas Blade

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

::: tip Seguro para invitados por diseño
Ambas directivas se resuelven de forma segura (devuelven `false`) para un usuario invitado - nunca lanzan una excepción. Puedes usar `@canin`/`@hasrolein` en vistas renderizadas antes de que la autenticación esté garantizada sin necesidad de envolverlas en una comprobación `@auth`.
:::

## Vía ContextService

Para usar en controladores, políticas, o jobs donde el modelo `User` puede no tener el trait `HasClearance`:

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

## Vía el facade Clearance

El facade `Clearance` delega en el mismo servicio - un punto de entrada global conveniente:

```php
Clearance::canIn($user, 'orders-create', $store);
Clearance::resolveFor($user, $store);
Clearance::guards(); // names of all managed authentication guards
```

Consulta la [referencia del facade](/es/api/facade) para la lista completa de métodos.

## Cómo funciona la resolución

`resolveFor()` combina tres fuentes, en este orden de precedencia:

1. **Concesiones de rol contextual** - permisos de los roles que el usuario posee específicamente dentro de este contexto (`clr_role_ctx`).
2. **Concesiones de rol global** - permisos de los roles Spatie asignados globalmente al usuario (de modo que `super_admin` y otros roles globales satisfacen `canIn()` para cualquier contexto).
3. **Overrides por contexto** (`clr_ctx_overrides`) - `forced_on` añade un permiso encima de lo anterior; `forced_off` lo elimina, incluso si el permiso fue concedido por un rol global.

::: danger Una denegación siempre gana
Si un permiso es `forced_off` para un usuario en un contexto determinado, esa denegación anula cualquier otra fuente - incluyendo una concesión de rol global y una concesión de rol contextual. No hay forma de volver a conceder un permiso `forced_off` para ese contexto salvo eliminando el propio override. Diseña tu política de overrides teniendo esto en cuenta: `forced_off` es la señal más fuerte del sistema.
:::

## Base de datos

| Tabla | Columnas clave |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - único por `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## Próximos pasos

- [Usuarios](/es/features/users) - la interfaz del panel para asignar roles contextuales y overrides.
- [Roles](/es/features/roles#scope-global-vs-contextual) - declarar un rol como `contextual` y sus `context_types` permitidos.
- [Referencia de traits y directivas](/es/api/traits-directives) - las firmas completas del trait `HasClearance` y de las directivas Blade.
- [Referencia de servicios](/es/api/services) - la API completa de `ContextService`.
