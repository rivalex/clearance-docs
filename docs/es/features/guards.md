# Guards

El panel de Guards gestiona los guards de autenticación como registros de base de datos, de modo que los guards pueden añadirse o modificarse sin editar `config/auth.php` y volver a desplegar.

## Qué hace el panel

- **Crear / editar / eliminar** guards, cada uno con un `name`, `driver`, y `provider`.

<Screenshot src="/screenshots/guards-page.png" alt="Panel de Guards de Clearance listando guards respaldados por base de datos" caption="El panel de Guards lista cada guard respaldado por base de datos con su driver y proveedor." />

## Cómo se aplican los guards

Los guards gestionados desde el panel se inyectan en `auth.guards` al arrancar la aplicación, de modo que el sistema de auth de Laravel los reconoce antes de que se ejecute cualquier request - sin necesidad de editar `config/auth.php` manualmente.

## Drivers en lista de permitidos

Solo se aceptan los drivers en la lista de permitidos:

```php
// config/clearance.php
'allowed_guard_drivers' => ['session', 'token', 'jwt', 'passport', 'sanctum'],
```

::: warning Los drivers no listados se omiten, no se rechazan de forma ruidosa
Un guard con un driver que no está en `allowed_guard_drivers` se omite al arrancar (con una advertencia en el log) en lugar de corromper la configuración de auth. Si un guard que creaste no está surtiendo efecto, comprueba primero el driver contra la lista de permitidos.
:::

## Control de acceso

El acceso de lectura requiere `clearance-access`. Crear, editar o eliminar un guard requiere `clearance-guards-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-guards-write');
```

## Ruta y componente incorporado

| URI | Nombre de ruta | Descripción |
|---|---|---|
| `/clearance/guards` | `clearance.guards` | Guard management (create/edit/delete) |

```blade
<livewire:clearance::guards.guard-manager />
```

## Base de datos

| Tabla | Columnas clave |
|---|---|
| `clr_guards` | `name` (único), `driver`, `provider` |

## Próximos pasos

- [Referencia de configuración](/es/guide/configuration) - las claves de configuración completas `allowed_guard_drivers` y `guards`.
- [Referencia del facade](/es/api/facade) - `Clearance::guards()` devuelve los nombres de todos los guards de autenticación gestionados.
