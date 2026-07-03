# Traits y Directivas

Referencia de los traits, concern, contrato, y directivas Blade que conforman la superficie de Clearance orientada al desarrollador fuera del facade y los servicios.

## `HasClearance`

`Rivalex\Clearance\Traits\HasClearance` — añádelo a tu modelo `User`.

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance;
}
```

Incluye internamente el `HasRoles` de Spatie, así que este único trait reemplaza a ambos — no añadas `Spatie\Permission\Traits\HasRoles` por separado.

Añade los siguientes métodos de instancia:

```php
$user->canIn('orders-create', $store);            // bool
$user->canIn('orders-create', $store, 'api');     // optional guard filter
$user->hasPermissionIn('orders-create', $store);  // alias of canIn()
$user->hasRoleIn('manager', $store);               // bool
$user->hasRoleIn('manager', $store, 'web');         // optional guard filter
```

| Método | Devuelve | Descripción |
|---|---|---|
| `canIn(string $permission, $context, ?string $guard = null)` | `bool` | Comprobación de permiso limitada a una instancia de contexto. |
| `hasPermissionIn(string $permission, $context, ?string $guard = null)` | `bool` | Alias de `canIn()`. |
| `hasRoleIn(string $role, $context, ?string $guard = null)` | `bool` | Comprobación de rol limitada a una instancia de contexto. |

::: tip
Sin este trait, estos métodos de instancia no están disponibles — pero las directivas Blade y `ContextService` siguen funcionando de todos modos. Ninguno depende del trait.
:::

## `HasPermissionGroups`

`Rivalex\Clearance\Concerns\HasPermissionGroups` — úsalo cuando necesites un modelo `Permission` personalizado (configurado vía `config('permission.models.permission')`) en lugar del propio `Rivalex\Clearance\Models\Permission` de Clearance.

Aplícalo para mantener funcionando en tu modelo personalizado los accessors de UI agrupada que respaldan el panel de Permisos:

```php
use Rivalex\Clearance\Concerns\HasPermissionGroups;

class Permission extends \Spatie\Permission\Models\Permission
{
    use HasPermissionGroups;
}
```

Proporciona estos accessors:

| Accessor / método | Descripción |
|---|---|
| `permission_group` | El prefijo de grupo antes del separador de nomenclatura (p. ej. `orders` en `orders-create`). |
| `group_string` | El grupo como cadena de visualización. |
| `abilities()` | El conjunto de habilidades/acciones definidas para el grupo del permiso. |
| `colorForAbility()` | Un color para la habilidad del permiso, usado por la UI del panel. |

Consulta [Permisos](/es/features/permissions.md) para cómo se usan la convención de nomenclatura y el agrupamiento en el panel.

## `ClearanceContextable`

`Rivalex\Clearance\Contracts\ClearanceContextable` — impleméntalo en un modelo de contexto (p. ej. tu modelo `Store`) para controlar cómo se etiqueta en el panel.

```php
interface ClearanceContextable
{
    public function clearanceLabel(): string;
}
```

Si un modelo de contexto no implementa este contrato, el panel recurre a `contextual_models.<FQCN>.label_attribute` (por defecto `name`) de `config/clearance.php`. Consulta [Configuración](/es/guide/configuration.md).

## Directivas Blade

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

| Directiva | Comprobación equivalente |
|---|---|
| `@canin($permission, $context)` | `ContextService::canIn()` |
| `@hasrolein($role, $context)` | `ContextService::hasRoleIn()` |

::: tip
Ambas directivas se resuelven de forma segura para un usuario invitado — devuelven `false` / omiten el bloque y nunca lanzan una excepción. Tampoco requieren el trait `HasClearance`; se resuelven a través del mismo `ContextService` subyacente que usa el facade.
:::

## Ver también

- [Facade](/es/api/facade.md) — el equivalente de llamada estática de estos métodos de instancia.
- [Servicios](/es/api/services.md) — los internos de `ContextService` y la precedencia de resolución de las 3 fuentes.
