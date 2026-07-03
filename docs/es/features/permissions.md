# Permisos

El panel de Permisos es una interfaz CRUD completa sobre el modelo `Permission` de Spatie. Clearance nunca introduce su propia tabla de permisos - cada permiso que creas aquí es un permiso de Spatie normal, utilizable en cualquier lugar donde se use la API de Spatie (`$user->can()`, `Gate::allows()`, políticas, `@can`).

## Qué hace el panel

- **Crear** - añade un nuevo permiso, validado contra la [convención de nomenclatura](#permission-naming-convention) (a menos que esté desactivada) y limitado a un guard.
- **Editar** - renombra un permiso o cambia su guard.
- **Eliminar** - elimina un permiso y sus asignaciones de rol/usuario (comportamiento de cascada estándar de Spatie).

<Screenshot src="/screenshots/permissions-page.png" alt="Panel de Permisos de Clearance listando permisos agrupados por prefijo de nomenclatura" caption="El panel de Permisos lista cada permiso de Spatie, agrupado por prefijo de nomenclatura." />

<Screenshot src="/screenshots/permission-form-modal.png" alt="Formulario de creación/edición de permisos con validación de la convención de nomenclatura" caption="El formulario de permisos valida la convención de nomenclatura group-action antes de guardar." />

## Convención de Nomenclatura de Permisos

Los permisos deben seguir el formato `group-action`:

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

El prefijo de grupo (`orders` en `orders-create`) es lo que el [Panel de Control](/es/features/dashboard) usa para contar los grupos de permisos distintos.

::: tip Desactivar la validación
Algunos equipos migran desde un esquema de nomenclatura diferente. Desactiva la comprobación por entorno:

```php
// config/clearance.php
'enforce_naming_convention' => false,
```
:::

## Control de acceso

El acceso de lectura al panel (ver la lista de permisos) requiere `clearance-access`. Crear, editar o eliminar un permiso requiere adicionalmente `clearance-permissions-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-permissions-write');
```

::: warning Los permisos `clearance-*` están reservados
Los propios permisos `clearance-*` (`clearance-access`, `clearance-permissions-write`, etc.) no pueden crearse, renombrarse ni eliminarse a través de este panel - ni siquiera por un actor `super_admin`. Se gestionan exclusivamente mediante el propio flujo de instalación del paquete. Consulta [Super Admin](/es/features/super-admin#reserved-clearance-permissions).
:::

## Ruta y componente incorporado

| URI | Nombre de ruta | Descripción |
|---|---|---|
| `/clearance/permissions` | `clearance.permissions` | Permission CRUD with group-prefix naming validation |

```blade
<livewire:clearance::permissions.permission-manager />
```

## Próximos pasos

- [Roles](/es/features/roles) - asigna permisos a un rol, limitado por guard.
- [Super Admin](/es/features/super-admin) - la tabla completa de permisos de escritura granulares.
- [Referencia de configuración](/es/guide/configuration) - `enforce_naming_convention`, `naming_separator`.
