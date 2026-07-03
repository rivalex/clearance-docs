# Configuración

Clearance viene con valores predeterminados sensatos, así que funciona de inmediato tras la [instalación](/es/guide/installation). Publica el archivo de configuración cuando necesites personalizar el prefijo de ruta, el middleware, los modelos contextuales, o cualquier otro ajuste.

```bash
php artisan vendor:publish --tag=clearance-config
```

Esto publica `config/clearance.php`.

## Referencia de configuración

Esta es la referencia definitiva de cada clave en `config/clearance.php`. El resto de páginas de esta documentación enlazan aquí en lugar de repetir la tabla.

| Clave | Predeterminado | Propósito |
|---|---|---|
| `route_prefix` | `'clearance'` | Prefijo de URI para todas las rutas del panel. |
| `middleware` | `['web', 'auth']` | Se aplica a todas las rutas del panel antes de `clearance.access`. |
| `access_permission` | `'clearance-access'` | Permiso verificado por el middleware de acceso. |
| `user_model` | `null` | Por defecto usa `auth.providers.users.model`. |
| `modules.users` | `false` | Interruptor maestro para el módulo de Usuarios (rutas + componentes Livewire). |
| `contextual_models` | `[]` | `FQCN => ['label', 'icon', 'label_attribute', 'searchable']` - modelos que pueden actuar como contextos de vinculación de rol. |
| `enforce_naming_convention` | `true` | Fuerza la convención de nomenclatura `group-action`. |
| `naming_separator` | `'-'` | Carácter separador - solo `'-'` o `'_'`. |
| `guards` | `[]` | Sobrescribe los guards auto-detectados desde `config/auth.php`. Vacío = auto-detecta todos. |
| `allowed_guard_drivers` | `['session', 'token', 'jwt', 'passport', 'sanctum']` | Drivers aceptados al inyectar guards de BD en `auth.guards`. |
| `layout` | `null` | Layout Blade para componentes de página completa. `null` = predeterminado de la app anfitriona (`config('livewire.layout')`). |
| `auto_assign_default_role` | `false` | Auto-asigna el rol predeterminado configurado en Configuración al evento `Registered`. |
| `super_admin_gate_bypass` | `false` | Opcional: `super_admin` evita todas las comprobaciones de Gate en toda la aplicación. |
| `ui.flux_pro` | `null` | `null` = auto-detecta vía `Flux::pro()`. |

## Convención de nomenclatura de permisos

Los permisos deben seguir el formato `group-action`:

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

Desactiva la validación por entorno con `enforce_naming_convention`:

```php
'enforce_naming_convention' => false,
```

::: warning El separador se valida al arrancar
El valor `naming_separator` es la única parte de esta configuración que llega a un fragmento SQL sin procesar (agrupación/ordenación de permisos), así que solo se aceptan `-` o `_`.
:::

## Personalizar el middleware

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

Los nombres de ruta permanecen estables sin importar los cambios de prefijo o middleware - consulta el [Inicio Rápido](/es/guide/quick-start) para la tabla completa de rutas.

## Relacionado

- [Inicio Rápido](/es/guide/quick-start) - montar el panel con esta configuración aplicada
- [Publicación de assets](/es/guide/publishing) - publicar traducciones y entender la clave `layout`
- [Guards](/es/features/guards) - cómo se usan `guards` y `allowed_guard_drivers`
- [Super Admin](/es/features/super-admin) - cómo funciona `super_admin_gate_bypass`
