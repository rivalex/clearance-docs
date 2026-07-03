# Facade

El facade `Clearance` (`Rivalex\Clearance\Facades\Clearance`) expone la API de autorización contextual del paquete como llamadas de estilo estático. Está respaldado por `src/Clearance.php`, que delega cada llamada a [`ContextService`](/es/api/services.md#contextservice).

```php
use Rivalex\Clearance\Facades\Clearance;
```

::: tip
Las directivas Blade y `ContextService` funcionan sin ningún cambio en tu modelo `User`. El facade es un envoltorio de conveniencia alrededor del mismo motor — consulta [Autorización Contextual](/es/features/contextual-authorization.md) para el concepto detrás de las comprobaciones limitadas a un contexto.
:::

## `canIn()`

Comprueba si un usuario posee un permiso limitado a una instancia de contexto.

```php
Clearance::canIn($user, 'orders-create', $store);      // bool
Clearance::canIn($user, 'orders-create', $store, 'api'); // optional 3rd arg: guard name filter
```

| Parámetro | Tipo | Descripción |
|---|---|---|
| `$user` | `Authenticatable` | El usuario a comprobar. |
| `string $permission` | `string` | Nombre del permiso, p. ej. `orders-create`. |
| `$context` | instancia de modelo | El contexto al que se limita el permiso, p. ej. una `Store`. |
| `?string $guard` | `string\|null` | Filtro opcional de nombre de guard. |

Devuelve `bool`.

## `hasPermissionIn()`

Alias de [`canIn()`](#canin), llamado así para reflejar la convención `hasPermissionTo()` de Spatie.

```php
Clearance::hasPermissionIn($user, 'orders-create', $store);
```

Devuelve `bool`.

## `hasRoleIn()`

Comprueba si un usuario posee un rol limitado a una instancia de contexto.

```php
Clearance::hasRoleIn($user, 'manager', $store);
```

Devuelve `bool`.

## `resolveFor()`

Resuelve todos los nombres de permiso efectivos que un usuario posee dentro de un contexto dado.

```php
Clearance::resolveFor($user, $store); // Collection<string>
```

Devuelve una `Collection<string>` de nombres de permiso. Consulta [`ContextService::resolveFor()`](/es/api/services.md#contextservice) para las reglas de precedencia de las 3 fuentes usadas para construir esta lista.

## `guards()`

Lista los nombres de todos los guards de autenticación gestionados por Clearance.

```php
Clearance::guards(); // Collection<string>
```

Devuelve una `Collection<string>`. Respaldado por [`GuardService`](/es/api/services.md#guardservice). Consulta la [página de la funcionalidad Guards](/es/features/guards.md) para cómo se configuran los guards y se inyectan en `auth.guards`.

## Ver también

- [Traits y Directivas](/es/api/traits-directives.md) — los métodos de instancia equivalentes vía `HasClearance`, y las directivas Blade seguras para invitados.
- [Servicios](/es/api/services.md) — el `ContextService` subyacente y los servicios relacionados.
